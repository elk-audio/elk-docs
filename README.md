# ELK Development Kit

This collection of documents is our introduction and manual for the ELK Development Kit.

The Development Kit consists of a software prototyping component, and the Raspberry ELK board, which will here be documented in that order.

You will be able to run the software component natively on Debian Linux, on the Virtual Machine disk image we will share with you, as well as on the Raspberry ELK board. Mostly you interact with all three in a very similar way, but if there are differences we will explicitly point them out.

## 1. Documentation

1. [ELK Overview](documents/elk_overview.md)

First comes an overview of all the components involved (ELK Linux, Sushi, Sensei, Twine, etc).

2. [ELK Examples Overview](documents/elk_examples_overview.md)

Then, a step-by-step tutorial after which you will be able to run, hear and control our Sushi's headless-DAW, hosting a synthesizer VST plugin.

3. [Getting Started With ELK Development Kit](documents/getting_started_with_development_kit.md)

A summary of the examples available to showcase ELK functionality, and a description of each.

4. [Sushi Configuration Format](documents/sushi_configuration_format.md)

When configuring Sushi to run with a new set of plugins, a configuration file needs to be created. This is documented in.

5. [Sushi Internal Plugins](documents/sushi_internal_plugins.md)

To connect external physical controls to plugin parameters, you also need to create a configuration file for our Sensei software.

6. [Sensei Configuration Format](documents/sensei_configuration_format.md)

Sensei comes with a bunch of internal plugins, mostly developed for internal testing purposes but some of them can find their way as utility tools in a more complex setup.

7. [Building Plugins for ELK](documents/building_plugins_for_elk.md)

This document is a short guide to help you through the needed steps to adapt and compile your plugins to run on ELK.

8. [MIND Development Kit Image](documents/mind_devkit_image.md)

With the Development Kit, we also provide a Linux Image, which you can use to get started with building and running your plugins. That image is documented below.

9. [ELK Development Board Setup](documents/elk_development_board_setup.md)

To run your plugins on the Development Kit Raspberry ELK board, you need to setup your board, and transfer your plugin and configurations to it.

## 2. Tech Articles

On the MIND Music Labs website, there are a number of Tech articles that cover important topics directly relevant for learning to work with our Development Kit:

1. [How ELK Works: A look Under the Hood of a Device Powered By ELK](https://www.mindmusiclabs.com/a-look-under-the-hood-of-a-device-powered-by-elk/)
2. [ELK Development Boards Overview](https://www.mindmusiclabs.com/development-board-overview/)
3. [Discover Sushi: The DAW Inside ELK](https://www.mindmusiclabs.com/daw-like-sushi/)
4. [Controlling Plug-Ins in ELK, Part1: Open Sound Control](https://www.mindmusiclabs.com/controlling-plug-ins-in-elk-part-1-open-sound-control/)
5. [Controlling Plug-Ins in ELK, Part2: Google Remote Procedure Call](https://www.mindmusiclabs.com/controlling-plug-ins-in-elk-part-2-google-remote-procedure-call-grpc/)

We also present technical articles more broadly relevant for audio development:

1. [Audio Latency Demystified, Part 1](https://www.mindmusiclabs.com/audio-latency-demystified-part-1/)
2. [Audio Latency Demystified, Part 2: Real-Time Linux Approaches](https://www.mindmusiclabs.com/audio-latency-demystified-part-2-4-real-time-linux-approaches/)
3. [Using CMake for Compiling XMOS Code](https://www.mindmusiclabs.com/using-cmake-for-compiling-xmos-code/)
4. [std::algorithm for Audio, Part 1](https://www.mindmusiclabs.com/stdalgorithm-for-audio/)
5. [std::algorithm for Audio, Part 2](https://www.mindmusiclabs.com/stdalgorithm-for-audio-part-ii/)

## 3. Examples

The Development Kit ships with a number of example plugins and configurations, which you should use to better learn what is possible with ELK:

1. MDA jx10 VST2 plugin example
2. MDA jx10 VST3 plugin example
3. MDA jx10 LV2 plugin example
4. ELK Multi FX Example

## 4. ELK Online Forum

Finally, for any questions or discussion on the ELK Development Kit, please refer to the [ELK Online Forum](https://forum.elkmusicos.com). Currently forum participation is invitation only - please get in touch with us at MIND if you do not yet have an account!