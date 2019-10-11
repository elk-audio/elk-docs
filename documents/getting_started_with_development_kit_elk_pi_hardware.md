# Getting Started With ELK Pi Development Kit  Hardware

# 1. Assembly

1. Mount the four f-m spacers into the f-f spacers on the Raspberry Pi (*Figure 1)*

![Figure 1](illustrations/elk_pi_assembly_step_1.png)

2. Plug the Sika on top of the Raspberry Pi (*Figure 2*).

   ![img](illustrations/elk_pi_assembly_step_2.png)

3. Add the four screws if you want extra stability (*Figure 3).*

![img](illustrations/elk_pi_assembly_step_3.png)

## 2. Flashing the Elk operating system image to the SD card

The SD card you received with the board is currently empty - you will have to flash it with an image provided by us, to get the board running Elk.

We did not pre-flash the cards, because with the Development Kit still being in beta, the image is subject to frequent changes.

1. **DOWNLOAD THE LATEST IMAGE FROM THE FOLLOWING LINK (64-BIT IMAGE FOLDER LINK PENDING)**. It will have a filename such as: *elk-sika-image-dev-raspberrypi3.wic.bz2*, and be about 6-7 GB in size.

2. Connect the empty SD card to a computer. If you do not have a computer with an SD/Micro-SD card reader, you will need a USB adapter, which we did not provide in the box.

3. If you are using Windows:

   1. Download and install [balenaEtcher](https://www.balena.io/etcher/), or the [Win32 Disk Imager](https://www.raspberrypi.org/downloads/).

4. If you are using Linux, use  [balenaEtcher](https://www.balena.io/etcher/), or follow the below bzcat instructions:

   1. Find out under what name your SD card is listed on your computer. It can be /devsda, or /sdb /sdc /sdd, or /mmcblk0.

   2. To flash onto the SD card run :
      *bzcat elk-sika-image-dev-raspberrypi3.wic.bz2 | sudo dd of=/dev/<name listed on your system\> bs=4M status=progress && sync*

      The process can take a few minutes, so this is a good time to stretch your legs!
      
      If for any reason the process fails, just reduce the *bs=4M* speed to 1M for example, and it should work great.

5. If you are using OS X:

   1. Also there you can flash the image using [balenaEtcher](https://www.balena.io/etcher/), or use the above bzcat instructions for Linux.

## 3. Power it up and log on...

1. Put the SD card in the corresponding slot on the Raspberry Pi.
2. You can:
   1. Connect over SSH, in which case you need to connect an Ethernet cable from the LAN port on your PC to the LAN port on the RPI. 
   2. Or, connect an HDMI screen, and a USB keyboard, directly to the Raspberry Pi's USB slots!
3. Connect the power supply Micro-USB cable to the Raspberry Pi.
4. If you connected the Pi directly to your computer's Ethernet port, set up a shared internet connection for that port - for example [following these instructions in the case of Linux](https://www.cesariogarcia.com/?p=611).
5. Assuming Linux, to see the IP address of the Raspberry Pi run "arp -a" . Then connect over SSH to the IP you find. On Windows, you can use Putty for SSH.
6. Once you have a terminal window, just log in, username mind, password elk.
7. You are ready to start making sound!

## 4. And run Sushi!

You will find several configurations and plugins ready to test on the image, under the folders "config_files" and "plugins" in the mind accounts user folder.

Unlike the VM, Sushi doesn't use JACK for audio on the Raspberry Pi, so you should instead run Sushi using the `-r` switch for selecting our RASPA low-latency front-end:

```bash
raspberrypi3:~$ sushi -r -c /home/mind/config_files/mda-vst2-configs/config_mda_synth.json 
```

Remember, you will always need to specify the full path to the config file you want to use, not relative.

If you need to connect USB MIDI devices, just plug them and connect them with the `aconnect` tool, as before:

```bash
# List available port for connections
raspberrypi3:~$ aconnect -l
# Take note of the port number X for your MIDI controller (e.g. 16) and the one Y assigned to Sushi
# (e.g. 128)
raspberrypi3:~$ aconnect X Y
# As an alternative to port numbers, it is possible to use the names reported by aconnect -l
```

If you connect a USB MIDI keyboard and a pair of headphones to the Elk Pi, you will now be able to play the MDA JX synthesizer running on the board!

Since this is a development board, you will need to start Sushi manually every time you want to play - but we will later show you how the device can be made to load it automatically on power-on, so that you can just power it on and play!

## 5. Powering Down

When developing for the board, the SD card is mounted for both and reading and writing, so do not just pull the power cord on the Rasberry Pi when you want to turn it off, or you risk corrupting the file-system!

Instead, type the following commands:

```bash
# get su access
raspberrypi3:~$ su
# tell Linux to shut down:
\h:\w$ shutdown now
```

## 6. Further steps

This guide serves only to get you up and running with the Elk Pi board.

You can do much more, such as connect it to WiFi, copy new plugins to it, as well as compile your own plugins for it. For all this, please refer to subsequent sections in the documentation!