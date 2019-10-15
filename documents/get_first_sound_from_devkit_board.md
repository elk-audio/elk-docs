# Run ELK on Development Kit Hardware

This guide expects that you have followed the previous Getting Started guide for your specific board to be ready to power up.

The following steps are general for all our development kit boards, covering the software, and how you get your first sound to play from the board.

## 1. Power it up, log on...

1. You can:
   1. Connect a monitor over HDMI, and a keyboard directly to the USB slot.
   2. Connect over SSH.
2. If your host board has an Ethernet port and you connected it directly to your computer (Elk Pi does, but not UpCore), set up a shared internet connection for your computers Ethernet port - for example [following these instructions in the case of Linux](https://www.cesariogarcia.com/?p=611).
3. Assuming Linux, to see the IP address of the board run `$ arp -a`. Then connect over SSH to the IP you find. On Windows, you can use [Putty](https://www.putty.org/) for SSH.
4. Once you have a terminal window, just log in: username `mind`, password `elk`.

(While you can keep using your board this way, you may also want to connect to it with a serial cable, or set up a WiFi connection, so that you can use SSH over those in the future. For instructions please refer to [Additional Development Kit Board Topics](devkit_further_topics.md)).

## 2. And run Sushi

1. You will find several configurations and plugins ready to test on the image, under the folders `~/config_files` and `~/plugins` in the user folder.

   Unlike the VM, Sushi doesn't use JACK for audio on the Raspberry Pi, so you should instead run Sushi using the `-r` switch for selecting our RASPA low-latency front-end:

```bash
$ sushi -r -c ~/config_files/mda-vst2-configs/config_mda_synth.json 
```

2. To connect USB MIDI devices, just plug them and connect them with the `aconnect` tool:

```bash
# List available port for connections
$ aconnect -l
# Take note of the port number X for your MIDI controller (e.g. 16) and the one Y assigned to Sushi
# (e.g. 128)
$ aconnect X Y
# As an alternative to port numbers, it is possible to use the names reported by aconnect -l
```

3. If you connect a USB MIDI keyboard and a pair of headphones to the Elk Pi, you will now be able to play the MDA JX10 synthesizer running on the board!

(Since this is a development board, you will need to start Sushi manually - but in [Additional Development Kit Board Topics](devkit_further_topics.md) we show how the device can be made to load a configuration automatically on power-on).

## 3. Powering Down

When developing for the board, the SD card is mounted for both and reading and writing, so do not just pull the power cord on the board when you want to turn it off, or you risk corrupting the file-system, meaning possible loss of data, and re-flashing the card.

Instead, type the following commands:

```bash
# get su access
$ su
# tell Linux to shut down:
\h:\w$ shutdown now
```

## 4. Further steps

Congratulations, you have now learned all the necessary first steps for working with the ELK development kit hardware and software!

There is however much further functionality, which you can learn about in the subsequent reference manual sections - most importantly for the development kit board, absolutely do read our [Additional Development Kit Board Topics](devkit_further_topics.md).