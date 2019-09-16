# Getting Started With ELK Development Kit

## Overview

Here you will quickly see which the first steps are, to run a plugin from within ELK's Sushi host, to get sound playing out of it, and to control its parameters using an Open Sound Control (OSC) GUI.

### Running a plugin within the Sushi host

You could run Sushi natively on your own Debian Linux computer, or through [this Virtual Machine Disk Image we provide](https://drive.google.com/open?id=12Tosw9DMljj20BPMYO2VVAfPygOf1VLH).

Either way, you need to download the Sushi AppImage from the [Downloads section of the elk-examples repository](https://bitbucket.org/mindswteam/elk-examples/downloads/). Read [these brief instructions if you are unfamiliar with running software in Linux AppImages](https://itsfoss.com/use-appimage-linux/).

For instructions on running and logging in to the Image, read the [MIND Development Kit Image](mind_devkit_image.md) section. Ensure that you have audio playback configured correctly for the virtual machine to play audio over JACK on your computer. This will vary depending on what your host Virtual Machine and Operating system is.

If you are running natively, you need to have Jack audio configured on your computer, which is beyond the scope of this tutorial - but if you have trouble getting it working [definitely ask us on the forum](https://forum.elkmusicos.com) and we'll do our best to get you going! In this example we also show using [Cadence and Catia for managing Jack](https://kx.studio/Repositories#Ubuntu), but you can use any other tools you prefer too.

#### Starting Sushi with the MDA jx10 vst synthesizer

First we assume you run Sushi either natively on Linux, or in the Virtual Machine provided.

The example used, requires that you have these files from our [elk-examples repository](https://bitbucket.org/mindswteam/elk-examples/src/master/):

1. config_play_vst3.json - the Sushi configuration file.
2. mda_jx10_vst3_open_stage_control_gui.json - the Open Stage Control GUI.
3. [mda-vst3.vst3.tar.xz](https://bitbucket.org/mindswteam/elk-examples/downloads/mda-vst3.vst3.tar.xz) - a build of the MDA plugins, containing also the JX10 Synthesizer binary.
4. (optionally) mda-vst3-touchosc-gui.touchosc, to control JX10 from the TouchOSC app.

First unpack the tar-file with the plugin to your local drive. Place the content in the default path expected: `/usr/lib/VST3/mda-vst3.vst3`. If you place the mda-vst3.vst3 extracted content elsewhere, you also need to edit the config_play_vst3.json to refer to that new path.

To run Sushi using JACK, and a configuration file provided by us for running the MDA JX10 vst3 synthesizer: 

1. Navigate to `/the/folder/where/you/have/placed/sushi` in a console window.

2. Type the following command:

```bash
$ ./Sushi-x86_64-0.7.0.AppImage -j --connect-ports -c /absolute/path/to/example/config/files/config_play_vst3.json
```

Note that with the current version of Sushi the path is expected to be absolute, a relative one will not work.

You should see the following status message:

```bash
SUSHI - Sensus Universal Sound Host Interface
Copyright 2016-2018 MIND Music Labs, Stockholm
```

3. Sushi should also appear as an audio device in your JACK configuration software - see below the Catia screen as an example:

![CATIA with SUSHI](illustrations/CATIA_with_SUSHI.png)

Note that its outputs are already connected.

That is because the `--connect-ports` command-line option used, attempts to connect the Sushi outputs. If after running they are not connected, you need to make these connections yourself, or you will not be able to hear sound.

If you were to instead run Sushi on the Development Kit Board, you should instead use the following command, this time using the `-r` switch for selecting the RASPA low-latency front-end instead of JACK:

```bash
$ sushi -r -c /absolute/path/to/your/config.json
```

#### Connecting MIDI to Sushi

Now, although Sushi is successfully running and hosting the MDA jx10 vst3 synthesizer plugin, you will need to connect it to a MIDI device to hear sound. For this, connect them with the `aconnect` tool:

1. Start a new terminal window than the one where Sushi is running.
2. Type `'aconnect -l'` to list all available MIDI devices and ports for connections:

```bash
client 0: 'System' [type=kernel]
    0 'Timer           '
    1 'Announce        '
client 14: 'Midi Through' [type=kernel]
    0 'Midi Through Port-0'
    1 'Midi Through Port-1'
    2 'Midi Through Port-2'
    3 'Midi Through Port-3'
client 24: 'UA-22' [type=kernel,card=2]
    0 'UA-22 MIDI 1    '
client 28: 'Launchkey MK2 25' [type=kernel,card=3]
    0 'Launchkey MK2 25 MIDI 1'
    1 'Launchkey MK2 25 MIDI 2'
client 128: 'Sushi' [type=user,pid=20167]
    0 'listen:in       '
    1 'write:out       '
```

3. The output you see will vary depending on your configuration, you should take note of the port number X for your MIDI controller (e.g. 28) and the one Y assigned to Sushi, here 128. 

4. By typing `aconnect X Y`, or for this example `aconnect 28 128`, you should now hear sound when playing on your keyboard. 

Tip: If you do not have a physical keyboard available, you can use the ***Virtual MIDI Piano Keyboard*** software (VMPK).

#### Controlling the MDA JX 10 VST 3 plugin over OSC, with Open Stage Control

If you do not know what Open Sound Control (OSC) is, it is helpful (but not mandatory) [if you first read the article on the MIND Music Labs Tech blog, dedicated to the topic](https://www.mindmusiclabs.com/controlling-plug-ins-in-elk-part-1-open-sound-control/).

1. Download and install the free [Open Stage Control](https://openstagecontrol.ammd.net/) application to your computer.

2. Upon starting it, you will see the screen below. Here, all you need to do, is enter the IP and Port, as is displayed under 'send' below. If you run the software on the same computer as you run Sushi, the IP should remain 127.0.0.1, and if you have not changed the Sushi default configuration, the Port should remain 24024.

   ![OpenStageControl_LaunchScreen](illustrations/OpenStageControl_LaunchScreen.png)

   3. Click Start, and subsequently 'Browse...', to navigate to the path where "mda_jx10_vst3_open_stage_control_gui.json" is available.
   4. You will now see the below GUI for controlling the jx10 synthesizer:

![OpenStageControl_with_jx10](illustrations/OpenStageControl_with_jx10.png)``

5. Assuming all is correctly configured, the controls on the screen will send OSC messages to to the jx10 synth via Sushi, altering its parameters.

For example, dragging the Knob 'Resonance', will send the floating point value to the OSC address  '/parameter/Synth/VCF_Reso'. 'Synth' is here the name given the plugin in the Sushi configuration file. 

Note: If you choose to change that name, the Open Stage Control  template is easily updated by  enabling editing, clicking on the "JX-10 ELK CONTROLLER"-title, and then, in the 'osc'-section of the editor, setting the 'address' field to "/parameter/your_synth_name", instead of the current "/parameter/Synth".

#### Controlling the MDA JX 10 VST 3 plugin over OSC, with TouchOSC

If you would prefer to control your plugin from an iPad/Android tablet, we have also created an example GUI using the [TouchOSC software](https://hexler.net/touchosc) - the file named `mda-vst3-touchosc-gui.touchosc`.

To use it, please refer to the TouchOSC manual - the only customisation needed is setting the IP-address that your computer running Sushi is using, in your TouchOSC app's settings.

![touch_osc_jx10_gui](illustrations/touch_osc_jx10_gui.png)