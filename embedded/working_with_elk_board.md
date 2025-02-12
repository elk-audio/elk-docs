# Working with Elk Board

There are several specific ways of working that you need to be aware of when developing with the Elk Linux platform, compared to when you are using another Linux device. These topics are collectively covered here.

## Filesystem

### Partition Layout

Elk uses a redundant partition layout to guarantee that the device will always boot in a working condition regardless of corruptions or incomplete updates due to e.g. power failures.

Therefore, it is important to remember to ***never put any files that you want to keep on the mounted root partition
!*** There is a separate partition mounted under */udata* for files that you want to preserve between system updates.

The internal eMMC storage on the board is divided into four partitions:

  * *boot*    : Boot partition with secondary bootloader and Kernel image
  * *rootfs1* : First copy of root filesystem
  * *rootfs2* : Second copy of root filesystem
  * *udata*   : User data partition

The reason for having two distinct root filesystem partitions is due to the power-off safe update mechanism through
 .swu files. At boot, only one of the two redundant filesystem is mounted (in */*). When a software update is run, it
  will put its contents on the root filesystem copy that is not currently mounted, and it will change the bootloader configuration to boot from it if the update is run successfully.

It is also worth noticing that while on development images all the partitions are mounted as read/write, in production images the root filesystems are usually mounted as read-only to minimise NAND wearing and possible corruptions when powering off the device.

Developer images can mount all partitions as read-write, but on production images the root filesystem is mounted as read-only to prevent corruptions and prevent NAND wearing.

### User Data Separation

Files in the user data partition under */udata*, remain untouched after running a software update, so keep any
 temporary files used for development there.

Also use  */udata* for the storage of settings & user patches.

If your plugin or other process needs to store data e.g. in the user home directory, a quick workaround is to create
 symbolic links to the */udata* partition from there.

## Elk Images

Very different configurations are needed on images for development, versus images deployed on released instruments. For that reason, we deploy and maintain two images:
* Development image
* Release image

For the Raspberry Pi community version we only provide the development image.

### Development Image

The development image contains the gcc toolchain, gdb & gdbserver, valgrind, tmux, full bash and common Linux tools. It is also not set up to auto-load an instrument configuration, and is easily switched between Read-Write and Read-Only modes.

### Release Image

The deployment/release image on the other hand is very streamlined, to minimise boot time and optimise stability. It
 is setup to auto-load, contains no development tools, replaces full-fat common Linux tools with their lightweight
  BusyBox alternatives. It is also setup to be read-only, with exception for */udata* if needed.

## Elk System Utilities

For important, common tasks with Elk, we provide the *elk_system_utils* script, which has commands for performing
 these common tasks straightforwardly. Type *elk_system_utils -h* to get a description of the various commands and
  their respective arguments.

### Changing audio buffer size
The audio buffer size of the system can be changed by typing *sudo elk_system_utils --set-buffer-size [size]*. The
 buffer size should be a power of 2. For example:

```bash
# set audio buffer size to 32
$ sudo elk_system_utils --set-buffer-size 32
```

The buffer size cannot be changed while a SUSHI session is running. Note that this setting is reset when a software update is performed.

### Enabling/Disabling Write Access to Root Partition

The default state on a newly flashed image is that the root partition is has both read and write permissions. However
, to have better reliability with SD Cards, you can set this to a read only filesystem. This is the default
 preference for Elk release images. You can change such permissions with *elk_system_utils*, temporarily or permanently:

#### Temporarily Change Write Access

Type *$ sudo elk_system_utils --remount-as-rw* to mount your current rootfs partition as read-write.

The command *$ sudo elk_system_utils --remount-as-ro*  to set read-only status.

Neither of these changes persist across boot cycles.

#### Permanently Change Write Access

Use *$ sudo elk_system_utils --ro-rootfs enable* to enable read-only access to current rootfs partition.

*$ sudo elk_system_utils --ro-rootfs disable*, permanently gives read-write access.

You need to perform a power cycle for either change to take effect.

### Manually Controlling Default/Fallback Root Partition

As described in the sections on the Elk filesystem and software update system, Elk has two root filesystem partitions
, *rootfs1* and *rootfs2*. It maintains a counter internally, for the number of failed boot attempts. Once that
 counter reaches 10, the two filesystems are automatically swapped, so that if a partition is corrupted, the device is still usable.

You can manually trigger this swap with the command *$ sudo elk_system_utils --default-rootfs 1* to set *rootfs1* as
 the default partition - or parameter 2 for *rootfs2*. The other partition is then automatically assigned as fallback.

To reset the boot count environment variable to 0, enter the command  *$ elk_system_utils --reset-boot-count*. This
 may be useful if you have for example yourself frequently disconnected power before booting completes.

## Connecting to Your Board

### Over WiFi

Log-in to the board using Ethernet, HDMI monitor and USB keyboard, or a serial cable, as per in our [getting started guide](../intro/run_elk_on_boards.md).

The easiest way to setup WiFi access is through *connman*, which is enabled by default in dev images:

```bash
$ sudo connmanctl
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
Besides root access, you can login as *mind* user (password: *elk*) which is the preferred one to run Sushi and other
 userspace applications.

Now finish with the command *$ sudo elk_system_utils --remount-as-ro*  to set filesystem status back to read-only.

If your file system is read-only, you can temporarily give yourself write access to the partition for these changes to be stored:

```bash
$ sudo elk_system_utils --remount-as-rw
```

Otherwise, you will need to complete the steps above every time you power on the board.


### Over a Serial Connection

You can also connect through the serial tty using a UART-to-USB converter with an FTDI chip. Follow the instructions on the relevant development board hardware datasheet to see how to connect the pins, and then use a serial communication program such as PuTTY, minicom or picocom.

For example, on a host Linux / macOS machine:

```bash
# the TTL converter usually shows up under /dev/ with a name starting with tty
$ picocom /dev/ttyXXX -b 57600 -l
```
Note: For ElkPi image version 0.6.0 and below use 115200 as baudrate.

With a serial connection you can login and have full shell access to the board, but files need to be transferred over
 the network or using a USB storage device. It might be useful to use *tmux* (installed in the development images) as
  a terminal multiplexer when working in this way.

## Monitoring Sushi Performance

Given Sushi is running, to get a rough view of CPU performance, *top*, *htop* and similar Linux tools will only show
 you the amount of CPU used in non-RT tasks and not the real time audio processing. To see the CPU usage of RT tasks, use:

```bash
$ watch -n 0.5 evl ps -t
```

For a more fine-grained analysis, you can use Sushi's gRPC api to query timing statistics of each track and plugin
, or you can run Sushi with the *--timing-statistics* flag to get the results on `timings.txt` file.

## Software Update System

Elk uses a modified version of [swupdate](https://sbabic.github.io/swupdate/) for its update system, which has double responsibility of restoring the system after filesystem corruption, and of securely updating it.

*Important note*: as written in the [Partition Layout section](#partition-layout), your data in the mounted root
 partition will be lost after an update! Keep anything that you want to save under */udata*.

### Updating over Network (WiFi / Ethernet)

  1. Make sure your board is connected to the same local network as your computer. If you need to setup a network, check the [relevant section](#connecting-to-your-board).
  2. Open a web browser and go to ***http://\<your Elk Pi board ip address\>:8080***
  3. You should see an Elk Audio OS update page. Just drag and drop the update file (*.swu*) or click in the middle
   to upload the file to start the update.
  4. When the update is completed successfully, the page prompts a message that the board is restarting, but you need
   to power off the board manually from the board's terminal (e.g. *sudo poweroff*).
  5. Turn on the board. You should see the updated version number on the splash logo, and the rootfs partition will be switched.

### Updating with an USB drive

  1. Elk updates are shipped in form of *.swu* files that can be put on a FAT32 formatted USB pendrive and inserted at any time.
  2. The update starts automatically once the USB drive is inserted in any of the RPi slots and the red and green LEDs present on the ElkPi hat will blink at the same time.
  3. Once the SWupdate is completed successfully, only the Green LED will be turned on.
  4. If the SWupdate failed for any reason, then only the Red LED will be turned on.
  5. In a failed SWupdate scenario, turn off the board and try again. If the problem persists, raise an issue on the [meta-elkpi](https://github.com/elk-audio/meta-elkpi) GitHub repository or ask on the forum.


## Configuring Automatic Startup

If you wish to have the board starting as an instrument automatically at startup, the suggested way is to use the systemD services that we provide.

1. Modify the file */lib/systemd/system/sushi.service* to provide the path to your JSON configuration
   and, in case, additional environment variables or Sushi command line flags.

   If you want automatic connection to a MIDI controller, the easiest way is just to modify the controller number
   /name in the script */usr/bin/connect-midi-apps*, which is started by the systemD service defined in */lib/systemd
   /system/midi-connections.service*.
2. Enable both Sushi and the MIDI connection service typing (as root):

```bash
$ systemctl enable sushi
$ systemctl enable midi-connections
```

Sushi will still output its log file in */tmp/sushi.log*. If you want to see the standard output as well, for example
 because you have put some debug printfs in your plugin, you can run *journalctl -fu sushi* (as root).

They can also be started temporarily with *$ systemctl start sushi* as any normal SystemD service.

### Automatic startup using user-autostart service

As an alternate way to start your application, especially if it requires more than running sushi, you can use the *user-autostart* systemD service.
This service is enabled by default and it looks for an executable script on */udata/autostart.sh*. If the script is found then the service will run it.

You can write the script to setup environment variables, run required processes and start sushi with a selected configuration.

Please keep in mind that the contents of */udata* is left unchanged by software upgrades, so the script needs to be updated manually.
This means that probably this method is not suitable for production but can be an easy way to get you started.

Please also be sure to disable the sushi systemD service if your autostart script runs sushi to avoid running multiple instances.
To disable sushi service run (as root):

```bash
$ systemctl disable sushi
```

To monitor the user-autostart service status you can run
```bash
systemctl status user-autostart
```

## Setting CPU Speed

The default CPU speed for the Raspberry Pi is set to it's highest possible setting by the Elk Audio OS.
This may not be the highest CPU speed the hardware is capable of, since we may choose to set it to a lower value to prevent overheating when working with an audio hat.

When running at that speed, there may be slightly more noise in the audio output, as well as more power drawn, and more heat produced.

If any of these are a problem for your particular application, we have included the option of swapping to a lower CPU speed with the command:

```bash
$ sudo elk_system_utils --set-cpu-speed min
```

The speeds supported are 800 and 1200 for the Pi4.

By simply typing:

```bash
$ sudo elk_system_utils
```

You will see all the options available on your current board.
