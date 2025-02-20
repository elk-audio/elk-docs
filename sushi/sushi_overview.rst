Sushi: The DAW Inside Elk
=========================

The Elk Music Operating System consists of many different parts. Here we focus on what we call **Sushi**,
the DAW that is at the core of audio and midi processing in the Elk MusicOS.

Main Features
-------------

Sushi is a track-based, headless Digital Audio Workstation. It works as
a plugin host, supporting multiple plugin standards. It features advanced
audio and midi routing, simple scripting setup, and is written to ensure
high performance and stability under low latency conditions. It can be
controlled through MIDI, OSC, and a gRPC interface.

**In a nutshell:**

-  Headless host with full run-time control over MIDI, gRPC and OSC interfaces.
-  Hosts VST 2.4, VST 3.7 and LV2  plugins.
-  Highly optimized for low latency performance.
-  Multithreaded audio processing support.
-  Ableton Link support.
-  Audio connections through Raspa, Jack, Portaudio, Core Audio and file I/O.
-  Simple scripting configuration.

Architecture
------------

Sushi shares the same basic architecture with most common DAWs like
Cubase or Ableton Live, having an unlimited number of parallel channels.
Most closely it is comparable to Logic Mainstage, in that it is a Live DAW.
Each track supports mono, stereo or up to 64 audio channels and as
many plugins as the CPU can handle. Pure MIDI tracks are also supported,
for instance outputting MIDI from an arpeggiator or step sequencer to an
external device.

There is also a multibus track mode with which a multichannel track can
have multiple stereo outputs, **each with their own individual gain and
panning controls**, which in turn can be routed to any audio output.
Useful for multi bus plugins. Sushi also features aux sends and
corresponding return tracks for effects processing.

In addition, Sushi allows adding pre and post tracks, each with as many
channels as there are audio inputs and outputs in sushi. They can be used
for pre-processing audio inputs before routing to individual tracks and
for adding master effects to the audio outputs.


Audio Frontends
---------------

Sushi supports multiple audio frontends:

-  **RASPA**: Sushi was built to work in perfect sync with Raspa, our
   proprietary low-latency audio framework.
-  `JACK Audio Toolkit <http://jackaudio.org/>`__: for running
   real-time code on normal Linux machines.
- **PortAudio**: For running realtime on macOS using Core Audio, Asio or 
   Directsound on Windows, or Linux using JACK or ALSA.
- **Core Audio** Native audio on macOS.
-  **Offline**: for processing audio and scripted event files (e.g. for
   automatic testing).
-  **Dummy**: without any connection to audio I/O, useful to debug some
   real-time safety issues on normal Linux machines.

**The ability to run Sushi with Jack, the most common audio framework on
Linux, makes it possible to run on almost any Linux system**. While it
doesn't give the same ultra low latency as running it with Raspa on an
Elk system. It does make it incredibly easy to test and develop plugins
and setups for Elk on a standard Linux machine. In fact, **almost all of
the development of Sushi has been done on standard Linux machines**.

When running with RASPA, PortAudio or Core Audio, Sushi is limited to the number
of inputs and outputs supported by the physical hardware. While when
running with Jack, Sushi exposes 8 input ports and 8 output ports that
can then be freely routed to physical outputs or inputs, or other Jack software.

The Offline frontend can be used for testing in environments that
lack audio codecs and for evaluating systems in a very early stage. It
has also proven to be very useful in debugging.

See Sushi's integrated usage help (by running *sushi -h* or
*sushi --help*), for command line options to choose one of the
frontends.

Configuration and Control
-------------------------

Sushi supports a number of protocols and technologies for controlling
and syncing with external sources and devices: MIDI, gRPC, Open Sound Control (OSC), Ableton Link.

As Sushi is a headless host, and intended for use in an embedded device,
it does not feature a graphical user interface. Instead, we have implemented several means of control,
which can be used together or separately to set Sushi up, and control it during run-time.

-  The initial setup is done through a `JSON <https://en.wikipedia.org/wiki/JSON>`__ configuration file.
-  At run-time, Sushi can be controlled via two API's available over the network: gRPC, and OSC.
-  End-users can additionally control Sushi and hosted plugins directly, using MIDI and Ableton Link, with the added flexibility of OSC for more advanced users.

.. figure:: ./illustrations/sushi_architecture.png
   :alt: img

Sushi's Public API Model
------------------------

While Sushi is accessible and configurable over several means, i.e. a json configuration file, gRPC and OSC,
the model they all control is the same.

It is thus best to describe this common model once below, and then address individual peculiarities for the
separate control avenues available.

Please refer to the dedicated section on the :ref:`sushi-public-api-model` for the full description.

JSON Configuration File
^^^^^^^^^^^^^^^^^^^^^^^

In the JSON configuration file it is possible to specify the initial number of tracks to use, their channel setup, the plugins on the track, audio
input and output routing, MIDI routing, which plugin parameters map to
Control Change messages, and so on. See below for a very simple example
for how to set up a synth plugin with MIDI-mapped parameters.

Sushi's JSON format is fully specified in a JSON schema, but it is
probably easier to learn by studying the provided examples, which cover
most common configurations, including multitrack/multichannel
configurations.

See our documentation on the `Sushi Configuration
Format <sushi_configuration_format.html>`__ for more details.

Run-Time Control
^^^^^^^^^^^^^^^^

The majority of run-time functionality is available using Google's gRPC,
which offers full control of all aspects of Sushi and hosted plugins,
with bidirectional communication to a remote client.

With the run-time gRPC API it is possible to perform the majority of tasks desired.
gRPC has bindings for most common programming languages. This gives total freedom to customise the behaviour and write
a complete GUI for Sushi in more or less any GUI framework of choice and account for multiple use cases.
Please see the dedicated subheading :ref:`sushi-control-grpc`,
for comprehensive information on Sushi's gRPC features.

An additional API based on `Open Sound Control <http://opensoundcontrol.org/>`__ (OSC) is available,
which covers a smaller subset of control for Sushi, than gRPC.
Sushi can both send OSC updates and receive notes and parameter changes through OSC.
Please see the dedicated subheading :ref:`sushi-control-osc` for more details on Sushi's OSC features.

End-User Control
^^^^^^^^^^^^^^^^

MIDI input and output is supported through
`ALSA <https://www.alsa-project.org/>`__, or RtMidi (for accessing CoreMIDI on macOS). This enables integration with
any class compliant midi device, like USB MIDI keyboards or controllers.
**A flexible routing system allows you to route MIDI based on channels
to any track**. MIDI can be freely routed to tracks and MIDI Program
Change and Control Change messages can be mapped to plugins and
parameters respectively. MIDI data can also be processed or generated by
plugins like sequencers. Sushi can output midi clock messages but not sync to incoming midi clock.

Sushi also features **tempo sync over Ableton Link**, which enables
you to seamlessly tempo sync Sushi with other devices over wifi. This
works with computers running `Ableton Live <https://www.ableton.com/>`__
or any Link enabled mobile app like `Reason
Compact <https://itunes.apple.com/se/app/reason-compact-make-music/id1253419004>`__
or `Korg
Gadget <https://www.korg.com/uk/products/software/korg_gadget/>`__.

Examples on Configuration and Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Used in combination, the above enable the creation of rich devices.

For some applications, user interactions will come from both front panel
knobs and a handheld device, i.e. a smartphone or tablet.
In that case, an app running on the handheld can use the gRPC interface to control Sushi.
Initial configurations are over

Other Elk devices could feature a built-in screen. In that case
the GUI will run on the same CPU as Sushi, though in a different
process. But as mentioned before, the GUI can be built in Python or any
other programming language of choice. **The dual kernel architecture of
Elk will guarantee that the graphics rendering will never interfere with
the audio dsp processing.**

Meanwhile, end-users can integrate your device with their other media devices,
using MIDI, Ableton Link, and OSC.

Discovering Initial Sushi Configuration's Available Parameters
--------------------------------------------------------------

These can be discovered in several ways.

The name, label, ID and OSC paths for the hosted plugins' parameters, is
dumped to stdout in JSON format when running Sushi with the flag
*--dump-plugins*. For example, to pipe the parameters for the LV2 JX10
example configuration into a .json file, type the following:

*$ sushi --dump-plugins -c /config_play_lv2_jx10.json > jx10_parameters.json*

This flag starts sushi with the dummy frontend and exits immediately
after dumping the parameters.

Parameter values are normalized to the range 0.0-1.0, floating point,
across plugin formats.

So, from the above example, to set the JX 10 synthesizers' resonance to 0.5
(halfway its range), send the following OSC message to Sushi (the
default UDP port for this is 24024):

*/parameter/jx10/VCF_Reso, f, 0.5*

The parameters can also be queried over gRPC's ParameterController,
documented under :ref:`sushi-control-grpc`.

Plugin Format Support
---------------------

-  Sushi can host plugins in Steinberg's **VST 2.4** and `VST
   3.7 <https://www.steinberg.net/en/company/technologies/vst3.html>`__
   formats, **LV2** (natively as well as using the LV2VST wrapper), plus
   an Internal plugin format, which all are abstracted in a generic
   "Processor" interface.

In order to load plugins in Sushi, they need to be compiled for the
system intended. Note that it is not possible to take an existing
Windows, macOS, or even native Linux plugin binary and load it in Elk.
That will not work. Though if the plugins are well written from the
start, porting them to Elk should be a rather straightforward process of
recompiling the plugins using our SDK.

Paths to plugin binaries can either be absolute or relative to the path set by the *--base-plugin-path* commmand line argument.

Threading
---------

Sushi can run its audio processing single threaded but also has **built
in multithreading support** to spread the audio processing over multiple
cores, depending on the type of system it is running on. To enable multicore audio processing, start sushi with the *-m/--multicore* option. 

When creating tracks it's possible to set the audio processing thread for that track using the *thread* argument. If the *thread* argument is not set, tracks will be allocated to threads according to a round-robin scheme. 

Note that thread is an abstract concept and does not directly map to physical CPU cores. If sushi is started with the *-m2" option, the audio processing with be split over 2 thread and the possible values for the thread argument is hence 0 and 1.

For Elk Audio OS systems it's possible to directly control which CPU cores sushi use for audio processing. See the Elk Audio OS documentation.

For developers that wish to utilize multithreading within a plugin, we have developed a small threading utility library that works with Elk and Sushi, called Twine.

Twine
-----

Twine is a C++ library that exposes some features of the underlying
Xenomai system to plugin developers, particularly accurate and real-time
safe timers, and multithreaded worker pools.

The library has a fall-back implementation for POSIX systems (tested on
standard Linux distros and macOS), which makes it convenient for
inclusion in an existing codebase. Twine also has limited functionality on Windows

Full source code is included in *work/twine* with Doxygen
documentation, unit tests and example code.

Logging
-------

On start, Sushi creates a log file in */tmp/sushi.log* where it logs
all relevant run-time information. Logging level and log destination can
be specified with the command line flag *-l* or *--log-level* and
*-l* or *-L* *-log-file=filename* respectively.

Configuring Sentry
------------------

Sushi can be built with sentry for crash handling by setting the `SUSHI_WITH_SENTRY`
CMake variable to ON.

The default DSN to which sentry will upload the minidumps and crash
reports, can be set with the `SUSHI_SENTRY_DSN` CMake variable, or overriden at
runtime with the `--sentry-dsn=<dsn.address>` flag when starting sushi.

To catch crashes sushi needs to know the location of the `crashpad_handler`
binary that is compiled along with sentry-native. The default location is
`./crashpad_handler` but this can be overriden with the
`--sentry-crash-handler=<path/to/crashpad_handler>` flag when starting sushi.

For sentry to symbolicate the crash logs, symbol files must be `uploaded to sentry`_.

More info on symbol files can be found `here`_.


.. _uploaded to sentry: https://docs.sentry.io/platforms/native/data-management/debug-files/upload/
.. _here: https://docs.sentry.io/platforms/native/data-management/debug-files/file-formats/

Running Sushi with a Different Buffer Size
------------------------------------------

Audio buffer-size is a compile-time option in Sushi, since on embedded
systems there's rarely the need from the user to adjust the buffer size
and in this way, the compiler has more room for optimizations.

However, Elk distributions are shipped with Sushi compiled at different
buffer sizes, usually [16, 32, 64, 128].

In case you want to use a different buffer size, you will also need to
change the audio driver parameter *audio_buffer_size* in the script
placed by default in */usr/bin/load-drivers* and reboot the board (or
restart the audio driver by removing and reinserting the module
*audio_rtdm*).
