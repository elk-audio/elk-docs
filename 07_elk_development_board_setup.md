# ELK development boards setup

This document contains basic information on how to connect to ELK development boards and configure
them to run audio plugins. See also the relevant datasheet for your ELK development board, e.g.
Rocket UpCore shield, for information on the hardware layout of the board.

## Installing an image to eMMC

If your boards came with the system already flashed to eMMC, you can skip this point. The
information here are written for the Rocket/UpCore development kit, for other systems there might be
few differences depending on how boot & interal storage are handled.

  1. Flash the installation image to a USB pendrive.

## Connecting over serial cable

Attach a serial cable from your PC to the board, using in case a provided TTL-USB adapter and
connect to the board using a terminal emulator of your choice. We recommend PuTTY under Windows and
picocom for Linux or macOS (available with Homebrew).

For example, on a host Linux / macOS machine:
```bash
# the TTL converter usually shows up under /dev/ with a name starting with tty
$ picocom /dev/ttyXXX -b 115200 -l
```

With a serial connection you can login and have full shell access to the board but it is not trivial
to transfer files over the network. It might be useful to use `tmux` (installed in the development
images) as a terminal multiplexer when working in this way.

## Set up WiFi connection

Log-in to the board as root (empty password in the dev image) using a serial cable or a HDMI monitor
+ USB keyboard.

On development images, you need to run the following command at the very first boot or anytime you
perform an update with a .swu file to flash a new development root filesystem:

```bash
$ rfkill unblock all
```

The easiest way to setup WiFi access is through `connman`, which is enabled by default in dev
images:

```bash
$ connmanctl
# Now you should see a connmanctl shell where to type these extra commands:
$ connmanctl> agent on
$ connmanctl> scan wifi
$ connmanctl> services
# The last command should have showed you a list of WiFi network available with their SSID and a
# long code starting with wifi_
$ connmanctl> connect wifi_xxx # choose the code for the desired network. You can tab-complete. 
$ connmanctl> Ctrl+D

# This will show you the IP address assigned to the board by DHCP
$ ip a
```

Then, you can just ssh to the board from your host computer or transferring files using scp / sftp.
Besides root access, you can login as `mind` user (password: `elk`) which is the preferred one to
run sushi and other userspace applications.

## Partition layout

The internal eMMC storage on the board is divided into four partitions:

  * `boot`    : Boot partition with secondary bootloader and Kernel image
  * `rootfs1` : First copy of root filesystem
  * `rootfs2` : Second copy of root filesystem
  * `udata`   : User data partition

The reason for two distinct root filesystem partitions is due to the power-off safe update mechanism
through .swu files. At boot, only one of the two redundant filesystem is mounted (in `/`). When a
software update is run, it will put its contents on the root filesystem copy that is not currently
mounted and it will change the bootloader configuration to boot from it if the update is run
successfully. 

For this reason, it is recommended that you put any file using for temporary development in the user
data partition, which will remain untouched after running a software update. It is also worth
noticing that while on development images all the partitions are mounted as read/write, in
production images the root filesystems are usually mounted as read-only to minimize NAND wearing and
possible corruptions when powering off the device.

## Testing a plugin using SUSHI

Follow the instructions in [Building Plugins for ELK](05_building_plugins_for_elk.md) to cross-compile a plugin for your target
boards. The development images include the toolchain, as well, so it's possible to build the plugin
locally on the board if you prefer so.

After you have the plugin binary, copy it on the board (preferably somewhere in /udata) and create a
SUSHI JSON config for it following one of the examples or looking at the provided JSON Schema for
more advanced configuration.

Then, log in as mind user and run sushi using the `-r` switch for selecting the RASPA low-latency
frontend:

```bash
$ sushi -r -c /path/to/your/config.json
```

If you need to connect USB MIDI devices, just plug them and connect them with the `aconnect` tool:

```bash
# List available port for connections
$ aconnect -l
# Take note of the port number X for your MIDI controller (e.g. 16) and the one Y assigned to Sushi
# (e.g. 128)
$ aconnect X Y
# As an alternative to port numbers, it is possible to use the names reported by aconnect -l
```

To get a rough view of CPU performance, `top`, `htop` and similar Linux tools will only show you
the amount of CPU used in non-RT tasks. To see CPU usage of Real Time tasks, use:

```bash
$ watch -n 0.5 cat /proc/xenomai/sched/stat
```

For a more fine-grained analysis, you can use SUSHI's gRPC API to query timing statistics of each
track and plugin, or you can run SUSHI with the `--timing-statistics` flag to get the results on
a file.

## Setting the board for automatic startup

If you wish to have the board starting SUSHI automatically at startup, the suggested way is to use
the systemD services that we provide.

  1. Modify the file `/lib/systemd/system/sushi.service` to provide the path to your JSON configuration
and, in case, additional environment variables or SUSHI command line flags.
  2. If you want automatic connection to a MIDI controller, the easiest way is just to modify the
controller number/name in the script `/usr/bin/connect-midi-apps`, which is started by the systemd
service defined in `/lib/systemd/system/midi-connections.service`
  3. Enable both SUSHI and the MIDI connection service typing (as root):

```bash
$ systemctl enable sushi
$ systemctl enable midi-connections
```

SUSHI will still output its log file in the working directory specified in the systemd service. If
you want to see the standard output as well, for example because you have put some debug printfs in
your plugin, you can run `journalctl -fu sushi` (as root) to check that as well.

