# Run Elk on Boards

This guide expects that you have followed the previous Getting Started guide for your specific board to be ready to power up.

The following steps are the same for all our development kit boards, covering the software, and how you get your first sound to play from the board.

## 1. Power it up, connect...

For getting access to the board terminal you have several options:

1. Connect a monitor over HDMI, and a keyboard directly to the USB slot.
2. Connect over SSH using Ethernet.
3. If the above two recommended options are not available to you, you can also connect through serial tty using a UART-to-USB converter with an FTDI chip - [read more here](working_with_elk_board.md).

For setting up SSH connections follow these tips:

1. If your host board has an Ethernet port and you connected it directly to your computer, set up a shared internet connection for your computers Ethernet port - for example [following these instructions in the case of Linux](https://www.cesariogarcia.com/?p=611).

2. Assuming Linux/macOS, you can connect to your board with the command *$ ssh mind@elk-pi.local*.
   1. If this fails, or if you want to connect to more than one board at the same time, you will need the IP address
    of your board(s). to see the IP address(es) run *$ arp -a* - then connect over SSH to the IP you find.
3. On Windows, you can use [Putty](https://www.putty.org/) for SSH.
4. Once you have a terminal window, just log in: username *mind*, password *elk*.

## 2. (If you have an HiFiBerry audio hat)

Elk supports audio hats from HiFiBerry, such as the ***DAC+ ADC*** and the ***DAC+ ADC Pro***.

If you are not using these, you can skip to step 3, and run Sushi.

If however you *are* using one of these, set the audio hat as follows:

```bash
# To use HiFiBerry DAC+ ADC audio hat
$ sudo elk_system_utils --set-audio-hat hifiberry-dac-plus-adc

# To use HiFiBerry DAC+ ADC Pro Hat.
$ sudo elk_system_utils --set-audio-hat hifiberry-dac-plus-adc-pro
```

After this, restart the system for these changes to take effect. Now you can run Sushi as described in step 3. More information on reconfiguring what hat to use is available in the [Working with your Elk Board](working_with_elk_board.md) section.

## 3. And run Sushi

You will find several configurations and plugins ready to test on the image, under the folders *~/config_files* and
 *~/plugins* in the user folder.

Unlike when running on a regular Linux machine, Sushi doesn't use JACK for audio on the Raspberry Pi, so you should
 run Sushi using the *-r* switch for selecting our RASPA low-latency front-end:

```bash
$ sushi -r --multicore-processing=2 -c ~/config_files/mda-vst3-configs/config_mda_synth.json &
```

Note the *&* at the end - this means the process starts in the background. When you later need to stop it, you type
 *$ pkill sushi*.

To connect USB Midi devices, just plug them and connect them with the *aconnect* tool:

```bash
# List available port for connections
$ aconnect -l
# Take note of the port number X for your MIDI controller (e.g. 16) and the one Y assigned to Sushi
# (e.g. 128)
$ aconnect X Y
# As an alternative to port numbers, it is possible to use the names reported by aconnect -l
```

If you connect a USB Midi keyboard and a pair of headphones to the Elk Pi, you will now be able to play the MDA JX10 synthesizer running on the board!

Since this is a development board, you will need to start Sushi manually - but under [Working with Elk](working_with_elk_board.md). we show how the device can be configured to load a setup automatically on power-on.

## 4. Powering down

In development releases of Elk, the storage is mounted for both reading and writing, so do not just pull the power cord on the board when you want to turn it off, or you risk corrupting the file-system.

Instead, type *$ sudo poweroff* to safely shutdown the system.

Elk releases for deployment are separate images, configured with a read-only filesystem, as detailed under [Working with Elk](working_with_elk_board.md).

## 5. Further steps

Congratulations, you have now learned all the necessary first steps for working with the Elk development kit hardware and software!

There is however much further functionality, which you can learn about in the subsequent reference manual sections - most importantly for the development kit board, absolutely do read our documentation on [Working with your Elk Board](working_with_elk_board.md).

