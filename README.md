# ELK Development Kit

This collection of documents is our introduction and manual for the ELK Development Kit.

The Development Kit consists of a software prototyping component, and the ELK Add-On board for the Raspberry Pi/UpCore, which will be documented here in that order.

You will be able to run the software component natively on a Debian Linux, on a virtual machine, as well as on the ELK development board. Mostly you interact with all three in a very similar way, but when there are differences we will explicitly point them out.

## 1. Documentation

1. [ELK Overview](documents/elk_overview.md):  
   An overview of all the components involved (ELK Linux, Sushi, Sensei, Twine, etc).  

### 1.1 Getting Started

These guides detail the minimum set of steps needed to get you up and running. There is however much more functionality in Elk, detailed in the subsequent reference manual.

1. [Get Started With the ELK Development Kit Software - Run your first plugin on a Linux machine](documents/getting_started_with_development_kit_software.md):  
     Step-by-step tutorial after which you will be able to run, hear and control our DAW hosting a VST3 synthesizer plugin on Linux.  
	​  
2. [Get Started With ELK Pi Development Kit Hardware](documents/getting_started_with_development_kit_elk_pi_hardware.md):
    Step-By-Step guide for getting the Elk Pi Hat working with a Raspberry Pi 3.

3. [Get Started With UpCore Development Kit Hardware](documents/getting_started_with_development_kit_upcore_hardware.md):  
    Step-By-Step guide for getting the UpCore Hat working with a Rocket/UpCore board.

4. [Start running Elk on Development Kit Hardware (common for all boards)](documents/get_first_sound_from_devkit_board.md):  
    Get your first sound - Step-By-Step guide for connecting audio to and from the hat, running Sushi, connecting MIDI and playing sound.

### 1.2 ELK Reference Manual

1. [Additional Development Kit Board Topics:](documents/devkit_further_topics.md)  
    Learn to connect to your board over serial cable or WiFi, monitoring the plugin performance, tweaking buffer sizes and setting up the board for automatic startup.  
	​  
	
2. [ELK Examples Overview](documents/elk_examples_overview.md):  
    A summary of the available examples which showcase Elk functionality, and a description of each.  
    ​  

3. [Sushi Configuration Format](documents/sushi_configuration_format.md):  
    When configuring Sushi to run with a new set of plugins, a configuration file needs to be created.  
    ​  

4. [Sushi Internal Plugins](documents/sushi_internal_plugins.md):  
    Sensei comes with a bunch of internal plugins, mostly developed for internal testing purposes but some of them can find their way into utility tools in a more complex setup.  
    ​  

5. [Sensei Configuration Format](documents/sensei_configuration_format.md):  
    To connect external physical controls to plugin parameters, you also need to write a configuration file for our Sensei software.  
    ​  

6. [Building Plugins for ELK](documents/building_plugins_for_elk.md):  
     This document is a short guide to help you through the needed steps to adapt and compile plugins to run on ELK.  
     ​

## 2. Tech Articles

On the Elk website, there are a number of Tech articles that cover important topics directly relevant for learning to work with our Development Kit:

1. [How ELK Works: A look Under the Hood of a Device Powered By ELK](https://www.elk.audio/a-look-under-the-hood-of-a-device-powered-by-elk/)
2. [ELK Development Boards Overview](https://www.elk.audio/development-board-overview/)
3. [Discover Sushi: The DAW Inside ELK](https://www.elk.audio/daw-like-sushi/)
4. [Controlling Plug-Ins in ELK, Part1: Open Sound Control](https://www.elk.audio/controlling-plug-ins-in-elk-part-1-open-sound-control/)
5. [Controlling Plug-Ins in ELK, Part2: Google Remote Procedure Call](https://www.elk.audio/controlling-plug-ins-in-elk-part-2-google-remote-procedure-call-grpc/)

We also present technical articles more broadly relevant for audio development:

1. [Audio Latency Demystified, Part 1](https://www.elk.audio/audio-latency-demystified-part-1/)
2. [Audio Latency Demystified, Part 2: Real-Time Linux Approaches](https://www.elk.audio/audio-latency-demystified-part-2-4-real-time-linux-approaches/)
3. [Using CMake for Compiling XMOS Code](https://www.elk.audio/using-cmake-for-compiling-xmos-code/)
4. [std::algorithm for Audio, Part 1](https://www.elk.audio/stdalgorithm-for-audio/)
5. [std::algorithm for Audio, Part 2](https://www.elk.audio/stdalgorithm-for-audio-part-ii/)

## 3. Examples

The Development Kit ships with a number of example plugins and configurations, which you should use to better learn what is possible with ELK:

1. MDA jx10 VST2 plugin example
2. MDA jx10 VST3 plugin example
3. MDA jx10 LV2 plugin example
4. ELK Multi FX Example

The examples can all be downloaded from our [private elk-examples bitbucket repository](https://github.com/elk-audio/elk-examples/src/master/).

## 4. ELK Online Forum

Finally, for any questions or discussion on the ELK Development Kit, please refer to the [ELK Online Forum](https://forum.elk.audio).
