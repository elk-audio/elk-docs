# Working with Elk

Learn to connect to your board over serial cable of WiFi, monitor the plugin performance, tweak buffer sizes, and set up the board for automatic startup.

## Connecting to Elk Board

### Over Serial Cable

Attach a serial cable from your PC to the board, using a TTL-USB adapter, and connect to the board using a terminal emulator of your choice. We recommend PuTTY under Windows and picocom for Linux or macOS (available with Homebrew).

For example, on a host Linux / macOS machine:

```bash
# the TTL converter usually shows up under /dev/ with a name starting with tty
$ picocom /dev/ttyXXX -b 115200 -l
```

With a serial connection you can login and have full shell access to the board, but files need to be transferred over the network or using a USB storage device. It might be useful to use `tmux` (installed in the development images) as a terminal multiplexer when working in this way.

### WiFi Connection

Log-in to the board as root (empty password in the dev image) using a serial cable or an HDMI monitor and USB keyboard.

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

Then, you can just ssh to the board from your host computer, and transfer files using scp / sftp.
Besides root access, you can login as `mind` user (password: `elk`) which is the preferred one to run Sushi and other userspace applications.

## Filesystem

### Software Update System

Elk uses a modified version of [swupdate](https://sbabic.github.io/swupdate/) for its update system, which has double responsibility of restoring the system after filesystem corruption, and of securely updating it.

Elk updates are shipped in form of `.swu` files that can be put on a FAT32 USB pendrive and inserted at any time. The update starts automatically and doesn't interrupt any of the other tasks since it will write its contents to a separate partition. It is possible to configure a network server to allow deployment of such files over-the-air using e.g. the internal WiFi connection.

### Partition Layout

Elk uses a redundant partition layout to guarantee that the device will always boot in a working condition regardless of corruptions or incomplete updates due to e.g. power failures.

Therefore, it is important to remember to ***never put any files that you want to keep on the mounted root partition!*** There is a separate partition mounted under `/udata` for files that you want to preserve between system updates.

The internal eMMC storage on the board is divided into four partitions:

- `boot`    : Boot partition with secondary bootloader and Kernel image
- `rootfs1` : First copy of root filesystem
- `rootfs2` : Second copy of root filesystem
- `udata`   : User data partition

The reason for having two distinct root filesystem partitions is due to the power-off safe update mechanism through .swu files. At boot, only one of the two redundant filesystem is mounted (in `/`). When a software update is run, it will put its contents on the root filesystem copy that is not currently mounted, and it will change the bootloader configuration to boot from it if the update is run successfully.

It is also worth noticing that while on development images all the partitions are mounted as read/write, in production images the root filesystems are usually mounted as read-only to minimise NAND wearing and possible corruptions when powering off the device.

Developer images can mount all partitions as read-write, but on production images the root filesystem is mounted as read-only to prevent corruptions and prevent NAND wearing.

### User Data Separation

Files in the user data partition under `/udata`, remain untouched after running a software update, so keep any temporary files used for development there. 

Also use  `/udata` for the storage of settings & user patches.

If your plugin or other process needs to store data e.g. in the user home directory, a quick workaround is to create symbolic links to the `/udata` partition from there.

## Monitoring Sushi Performance

Given Sushi is running, to get a rough view of CPU performance, `top`, `htop` and similar Linux tools will only show you the amount of CPU used in non-RT tasks and not the real time audio processing. To see the CPU usage of RT tasks, use:

```bash
$ watch -n 0.5 cat /proc/xenomai/sched/stat
```

For a more fine-grained analysis, you can use Sushi's gRPC api to query timing statistics of each track and plugin, or you can run Sushi with the `--timing-statistics` flag to get the results on a file.

## Configuring Automatic Startup

If you wish to have the board starting Sushi automatically at startup, the suggested way is to use the systemD services that we provide.

1. Modify the file `/lib/systemd/system/sushi.service` to provide the path to your JSON configuration
   and, in case, additional environment variables or Sushi command line flags.
   
   If you want automatic connection to a MIDI controller, the easiest way is just to modify the controller number/name in the script `/usr/bin/connect-midi-apps`, which is started by the systemD service defined in `/lib/systemd/system/midi-connections.service`
2. Enable both Sushi and the MIDI connection service typing (as root):

```bash
$ systemctl enable sushi
$ systemctl enable midi-connections
```

Sushi will still output its log file in `/tmp/sushi.log`. If you want to see the standard output as well, for example because you have put some debug printfs in your plugin, you can run `journalctl -fu sushi` (as root).

They can also be started temporarily with `systemctl start sushi` as any normal SystemD service.