# Elk Overview

This document is a quick start guide written primarily for audio plugin developers that want to get up to speed in porting and testing a VST plugin on Elk development boards.

It explains what the core components included in an Elk Linux distribution are, their configuration and the developer tools available. The focus is mostly on real-time safety issues of audio plugins and on how to spot and solve them in order to guarantee smooth execution of the RT audio path.

## Elk Linux Distribution

At its base, Elk is a custom embedded Linux distribution built using tools from the [Yocto project](https://www.yoctoproject.org/) and based on a minimal configuration of the [Poky reference distribution](https://www.yoctoproject.org/software-item/poky/). This document assumes the version targeted for Elk's evaluation boards based on [AAEON UpCore module](https://www.aaeon.com/en/p/iot-gateway-maker-boards-up-core) running on an Intel Cherry Trail Atom X5-Z8350.

The core components of the Elk distribution on the target board are:

  + **Elk Linux Kernel**, based on _Linux 4.19.x_ LTS series and the Xenomai I-Pipe patch

  + **Low-latency audio driver** based on such kernel

  + **Sushi**, a headless plugin host that interfaces with the low-latency driver and Linux MIDI subsystem

  + **TWINE**, a shared library that can be used by plugin developers to easily access features of the underlying RTOS such as high-level parallel task management, accurate timing information, etc.

  + **Sensei**, a hardware abstraction layer for physical controls attached to GPIOs such as buttons, knobs, LEDs, etc.

  + **ble-grpc-bridge**, an adapter that exposes gRPC endpoints as Bluetooth Low-Energy GATT characteristics

  + **systemd base configuration** for all the services needed, with two modalities (development and production)

  + **update system** based on swupdate for robust, poweroff safe system updates

On top of that, Elk packages a small set of common tools often needed in embedded Linux systems, such as BusyBox, a minimal Python 2.7 installation, hostapd and dhcpd for WiFi hotspot configuration and several others. Other tools are included in the development-only image, like gcc toolchain, gdb & gdbserver, valgrind, tmux, full bash and common tools instead of BusyBox. Packages that are available as recipes in [OpenEmbedded Core](https://layers.openembedded.org/layerindex/branch/master/recipes/) can be usually easily add to the image on request.

## Sushi

Sushi is a headless plugin host for Elk. It interfaces to the relevant system IOs (audio & MIDI) and run parallel processing tracks, each one with its plugin chain processed in series. It supports multiple audio backends:

  + **RASPA**, a userspace library for accessing the low-latency driver on target boards (statically linked into Sushi and currently not available separately)
  + **JACK Audio Toolkit**, for running real-time code on normal Linux machines
  + **Offline**, for processing audio and scripted event files (e.g. for automatic testing)
  + **Dummy**, without any connection to audio I/O, useful to debug some real-time safety issues on normal Linux machines

See Sushi's integrated usage help (by running `sushi -h` or `sushi --help`) for command line options to choose one of the backends.

Sushi can be controlled statically with JSON configuration files and, at run-time, via an API available over the network. At the moment, an API based on OSC is available and a preliminary version of a more complex one using Google's gRPC that offers full control and bidirectional communication to a remote client.
With the run-time RPC API it is possible to perform common tasks such as control of plugins automation parameters, track and mixing controls, adding/removing plugin to tracks, query the engine for track level meters or CPU usage by plugin, etc. The documentation inside Sushi folder shipped on the VM has more details, including full gRPC API definition in protobuf format.

Other relevant features are:

  + Steinberg's VST 2.x, VST3.x and Propellerhead's Rack Extension hosting (plus an internal plugin format), which all are abstracted in a generic "Processor" interface
  + Multichannel plugin hosting, with any number of input and output channels
  + Bus routing between audio tracks and physical audio inputs/outputs
  + Track controls: gain, pan, bypass, mute
  + ALSA MIDI support with powerful mapping to the hosted plugins
  + Sample accurate timestamping of incoming events
  + MIDI tempo sync from MIDI clock or Ableton Link
  + Multicore rendering of parallel tracks

Sushi's JSON format is fully specified in a JSON schema (also included in the VM image) but it is probably easier to just look at the provided examples for a quick start that covers most common configurations, including some multitrack/multichannel examples.

On start, Sushi creates a log file in /tmp/sushi.log where it logs all relevant run-time information. Logging
level and log destination can be specified with the command line flag `-l` or `--log-level` and `-l` or `-L ` `-log-file=filename` respectively. 

## Twine

Twine is a C++ library that exposes some features of the underlying Xenomai system to plugin developers, particularly accurate and real-time safe timers and multithreaded worker pools.
The library has a fallback implementation for POSIX systems (tested on standard Linux distros and macOS), which makes it convenient for inclusion in an existing codebase.
Full source code (MIT licensed) is included in `work/twine` with Doxygen documentation, unit test and example code.

## Sensei

Sensei is a hardware abstraction daemon that simplifies the access to the GPIOs available on the development board, offering OSC endpoints for event reporting and control (gRPC API is under development and will be available soon).

The hardware configuration of the hardware pins can be declared in a JSON configuration file. See [Sensei Configuration Format](sensei_configuration_format.md) for more details about the format and the available hardware sensor & output types.

On the UpCore based Elk development board, Sensei needs Sushi to be running to communicate with the GPIOs, since they both share a single SPI connect with the XMOS controller that handles both the audio controllers and the GPIOs. This limitation is not present on i.MX7 or Raspberry Pi based systems.

Sensei uses a logging system similar to Sushi for reporting run-time messages.

## Linux System configuration

As most modern Linux distributions, Elk adopts SystemD for its init system and other general tasks. By default, on the UpCore-based Elk development board, only the services to initialize the audio drivers and the update system are started.
There are services definitions for both Sushi and Sensei that can be enabled with `systemctl enable sushi` or started temporarily with `systemctl start sushi` as any normal SystemD service.

These example services start the applications with a trivial default configuration. To change the file used at startup, it is needed to modify the SystemD startup script in `/lib/systemd/system`, where it is also possible to add other relevant changes depending on the particular setup, e.g. setting the working directory or passing environment variables that can be read by plugins instatiated by Sushi.

## Filesystem organisation and Software Update system

Elk uses a modified version of [swupdate](https://sbabic.github.io/swupdate/) for its update system, using a redundant partition layout to guarantee that the device will always boot in a working condition regardless of corruptions or incomplete updates due to e.g. power failures.

Therefore, it is important to remember to *never put any files that you want to keep permanent on the mounted root partition!*. There is a separate partition mounted under `/udata` for files that you want to preserve between system updates. If your plugin or other process for some reason needs to store data e.g. in the user home directory, a quick workaround is to create symbolink links to the `udata` partition from there.

Developer images mount all partitions as read-write, but on production images the root filesystem is mounted as read-only to prevent corruptions and prevent NAND wearing.

Elk updates are shipped in form of `.swu` files that can be put on a FAT32 USB pendrive and inserted at any time. The update starts automatically and doesn't interrupt any of the other tasks since it will write its contents to a separate partition. It is possible to configure a network server to allow deploy of such files over-the-air using e.g. the internal WiFi connection.

## BLE-gRPC bridge

In order to communicate with e.g. a mobile device, Elk includes a daemon that wraps the gRPC endpoints of Sushi and Sensei transparently and with minimal overhead over Bluetooth Low-Energy GATT characteristics. It is based on recent versions (5.43 and later) of the standard BlueZ stack for Linux plus some direct communication with Bluetooth controllers using the HCI interface to optimize the connection parameters for both Android and iPhone mobile devices.

There is also a file transfer service to overcome BLE limitations on packet size and that can be used to transfer larger data than a normal API call, although it is practically limited by the low-bandwidth of BLE (10-20 Kbyte/s for Bluetooth 4.0).

In case you would need to use such system to communicate directly with your plugins, at the moment we offer to custom adaptations of this daemon to our customers. An open API is planned to be released in late 2019 to allow developers to independently hook their gRPC services.

