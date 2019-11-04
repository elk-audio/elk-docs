# Additional Development Kit Topics

Learn to connect to your board over serial cable of WiFi, monitoring the plugin performance, tweaking buffer sizes and setting up the board for automatic startup.

## Connecting over serial cable

Attach a serial cable from your PC to the board, using a TTL-USB adapter and connect to the board using a terminal emulator of your choice. We recommend PuTTY under Windows and picocom for Linux or macOS (available with Homebrew).

For example, on a host Linux / macOS machine:

```bash
# the TTL converter usually shows up under /dev/ with a name starting with tty
$ picocom /dev/ttyXXX -b 115200 -l
```

With a serial connection you can login and have full shell access to the board but files need to be transfered of the network or using a USB storage device. It might be useful to use `tmux` (installed in the development images) as a terminal multiplexer when working in this way.

## Set up WiFi connection

Log-in to the board as root (empty password in the dev image) using a serial cable or a HDMI monitor

- USB keyboard.

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
Besides root access, you can login as `mind` user (password: `elk`) which is the preferred one to run sushi and other userspace applications.

## Partition layout

The internal eMMC storage on the board is divided into four partitions:

- `boot`    : Boot partition with secondary bootloader and Kernel image
- `rootfs1` : First copy of root filesystem
- `rootfs2` : Second copy of root filesystem
- `udata`   : User data partition

The reason for having two distinct root filesystem partitions is due to the power-off safe update mechanism through .swu files. At boot, only one of the two redundant filesystem is mounted (in `/`). When a software update is run, it will put its contents on the root filesystem copy that is not currently mounted and it will change the bootloader configuration to boot from it if the update is run successfully. 

For this reason, it is recommended that you put any file using for temporary development in the user
data partition, which will remain untouched after running a software update. It is also worth noticing that while on development images all the partitions are mounted as read/write, in production images the root filesystems are usually mounted as read-only to minimize NAND wearing and possible corruptions when powering off the device.

## Running another plugin using Sushi

Follow the instructions in [Building Plugins for ELK](building_plugins_for_elk.md) to cross-compile a plugin for your target boards. The development images include the toolchain as well, so it's possible to build the plugin locally on the board if you prefer.

After you have the plugin binary, copy it to the board (preferably somewhere in /udata) and create a Sushi configuation file for it following one of our examples or looking at the provided JSON Schema for more advanced configuration.

## Monitoring Sushi performance

Given Sushi is running, to get a rough view of CPU performance, `top`, `htop` and similar Linux tools will only show you the amount of CPU used in non-RT tasks and not the real time audio processing. To see the CPU usage of RT tasks, use:

```bash
$ watch -n 0.5 cat /proc/xenomai/sched/stat
```

For a more fine-grained analysis, you can use Sushi's gRPC api to query timing statistics of each track and plugin, or you can run Sushi with the `--timing-statistics` flag to get the results on a file.

## Running Sushi at a different buffer size

Audio buffer-size is a compile-time option in Sushi, since on embedded systems there's rarely the need from the user to adjust the buffer size and in this way, the compiler has more room for optimisations.

However, Elk distributions are shipped with Sushi compiled at different buffer sizes, usually [16, 32, 64, 128]. The main command `sushi` is a wrapper around them that, when passed the option `-b N` as the first argument, select which one to run [default=64].

In case you want to use a different buffer size, you will also need to change the audio driver parameter `audio_buffer_size` in the script placed by default in `/usr/bin/load-drivers` and reboot the board (or restart the audio driver by removing and reinserting the module `audio_rtdm`).

## Setting the board for automatic startup

If you wish to have the board starting Sushi automatically at startup, the suggested way is to use the systemD services that we provide.

1. Modify the file `/lib/systemd/system/sushi.service` to provide the path to your JSON configuration
   and, in case, additional environment variables or Sushi command line flags.
   2. If you want automatic connection to a MIDI controller, the easiest way is just to modify the controller number/name in the script `/usr/bin/connect-midi-apps`, which is started by the systemd service defined in `/lib/systemd/system/midi-connections.service`
2. Enable both Sushi and the MIDI connection service typing (as root):

```bash
$ systemctl enable sushi
$ systemctl enable midi-connections
```

Sushi will still output its log file in /tmp. If you want to see the standard output as well, for example because you have put some debug printfs in your plugin, you can run `journalctl -fu sushi` (as root) to check that as well.