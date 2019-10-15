# Getting First Sound From Development Kit Hardware

This guide expects that you have followed the steps for getting your specific board ready to power up. The following steps are general for all our development kit boards.

## 1. Power it up, log on...

1. You can:
   1. Connect over SSH, in which case you need to connect an Ethernet cable from the LAN port on your PC to the LAN port on the host board.
   2. Or, connect a monitor over HDMI, and a keyboard directly to the USB slot!
2. If you connected the host board directly to your computer's Ethernet port, set up a shared internet connection for that port - for example [following these instructions in the case of Linux](https://www.cesariogarcia.com/?p=611).
3. Assuming Linux, to see the IP address of the board run `$ arp -a`. Then connect over SSH to the IP you find. On Windows, you can use [Putty](https://www.putty.org/) for SSH.
4. Once you have a terminal window, just log in: username `mind`, password `elk`.
5. You are ready to start making sound!

## 2. And run Sushi!

You will find several configurations and plugins ready to test on the image, under the folders `~/config_files` and `~/plugins` in the user folder.

Unlike the VM, Sushi doesn't use JACK for audio on the Raspberry Pi, so you should instead run Sushi using the `-r` switch for selecting our RASPA low-latency front-end:

```bash
raspberrypi3:~$ sushi -r -c ~/config_files/mda-vst2-configs/config_mda_synth.json 
```

Remember, you will always need to specify the full path to the config file you want to use, not relative.

If you need to connect USB MIDI devices, just plug them and connect them with the `aconnect` tool:

```bash
# List available port for connections
raspberrypi3:~$ aconnect -l
# Take note of the port number X for your MIDI controller (e.g. 16) and the one Y assigned to Sushi
# (e.g. 128)
raspberrypi3:~$ aconnect X Y
# As an alternative to port numbers, it is possible to use the names reported by aconnect -l
```

If you connect a USB MIDI keyboard and a pair of headphones to the Elk Pi, you will now be able to play the MDA JX10 synthesizer running on the board!

Since this is a development board, you will need to start Sushi manually every time you want to play - but we will later show you how the device can be made to load it automatically on power-on, so that you can just power it on and play!

## 5. Powering Down

When developing for the board, the SD card is mounted for both and reading and writing, so do not just pull the power cord on the board when you want to turn it off, or you risk corrupting the file-system, meaning possible loss of data, and re-flashing the card!

Instead, type the following commands:

```bash
# get su access
raspberrypi3:~$ su
# tell Linux to shut down:
\h:\w$ shutdown now
```

## 6. Further steps

Congratulations, you can now work with your Elk development board! 

There are several additional steps you can take however.