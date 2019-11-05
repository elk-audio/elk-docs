# Elk Development Kit

This collection of documents is our introduction and manual for the Elk Development Kit.

The Development Kit consists of a software prototyping component, and the Elk Add-On board for the Raspberry Pi/UpCore, which will be documented here in that order.

You will be able to run the software component natively on a Debian Linux, on a virtual machine, as well as on the Elk development board. Mostly you interact with all three in a very similar way, but when there are differences we will explicitly point them out.

## 1. Documentation

1. [Elk Overview](documents/elk_overview.md):  
   An overview of all the components involved (Elk Audio OS, Sushi, Sensei, Twine, etc).  

### 1.1 Getting Started

These guides detail the minimum set of steps needed to get you up and running. There is however much more functionality in Elk, detailed in the subsequent reference manual.

1. [Elk Development Kit Software - Run your first plugin on a Linux machine](documents/getting_started_with_development_kit_software.md):  
     Step-by-step tutorial after which you will be able to run, hear and control our DAW hosting a VST3 synthesizer plugin on Linux.  
	​  
2. [Elk Pi Development Kit Hardware](documents/getting_started_with_development_kit_elk_pi_hardware.md):
    Step-By-Step guide for getting the Elk Pi board working with a Raspberry Pi 3.  
	​  
3. [UpCore Development Kit Hardware](documents/getting_started_with_development_kit_upcore_hardware.md):  
    Step-By-Step guide for getting the UpCore board working with a Rocket/UpCore board.  
	​  
4. [Run Elk on Development Kit Hardware (common for all boards)](documents/get_first_sound_from_devkit_board.md):  
    Get your first sound - Step-By-Step guide for connecting audio to and from the board, running Sushi, connecting MIDI and playing sound.  
	​ 

### 1.2 Elk Reference Manual

1. [Additional Development Kit Board Topics:](documents/devkit_further_topics.md)  
    Learn to connect to your board over serial cable or WiFi, monitoring the plugin performance, tweaking buffer sizes and setting up the board for automatic startup.  
	​  
	
2. [Elk Examples Overview](documents/elk_examples_overview.md):  
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

6. [Building Plugins for Elk](documents/building_plugins_for_elk.md):  
     This document is a short guide to help you through the needed steps to adapt and compile plugins to run on Elk.  
     ​

## 2. Tech Articles

On the Elk website, there are a number of Tech articles that cover important topics directly relevant for learning to work with our Development Kit:

1. [How Elk Works: A look Under the Hood of a Device Powered By Elk](https://elk.audio/how-elk-works/)
2. [Elk Development Boards Overview](https://elk.audio/elk-development-boards-overview-3/)
3. [Discover Sushi: The DAW Inside Elk](https://elk.audio/daw-like-sushi/)
4. [Controlling Plug-Ins in Elk, Part1: Open Sound Control](https://elk.audio/controlling-plug-ins-in-elk-part-i/)
5. [Controlling Plug-Ins in Elk, Part2: Google Remote Procedure Call](https://elk.audio/controlling-plug-ins-in-elk-part-ii/)

## 3. Examples

The Development Kit ships with a number of example plugins and configurations, which you should use to better learn what is possible with Elk:

1. MDA jx10 VST2 plugin example
2. MDA jx10 VST3 plugin example
3. MDA jx10 LV2 plugin example
4. Elk Multi FX Example

The examples can all be downloaded from our [GitHub elk-examples repository](https://github.com/elk-audio/elk-examples/).

## 4. Elk Online Forum

Finally, for any questions or discussion on the Elk Development Kit, please refer to the [Elk Online Forum](https://forum.elk.audio).
