# ELK Development Kit Introduction #

This collection of documents is our introduction and manual for the ELK Development Kit.

The Development Kit consists of a software prototyping component, and the Raspberry ELK board, which will here be documented in that order.

## 1. Documentation

First comes an overview of all the components involved (ELK Linux, SUSHI, SENSEI, TWINE, etc):

1. [ELK Overview](01_elk_overview.md).

Then, a step-by-step tutorial after which you will be able to run, hear and control our SUSHI headless-DAW, hosting a synthesizer VST plugin:

2. [Getting Started With ELK Development Kit.](02_getting_started_with_development_kit.md)

When configuring SUSHI to run with a new set of plugins, a configuration file needs to be created. This is documented in:

3. [SUSHI Configuration Format](03_sushi_configuration_format.md).

To connect external physical controls to plugin parameters, you also need to create a configuration file for our SENSEI software.

4. [SENSEI Configuration Format.](04_sensei_configuration_format.md)

This document is a short guide to help you through the needed steps to adapt and compile your plugins to run on ELK:

5. [Building Plugins for ELK](05_building_plugins_for_elk.md).

With the Development Kit, we also provide a Linux Image, which you can use to get started with building and running your plugins. That image is documented below:

6. [MIND Development Kit Image](06_mind_devkit_image.md).

Now, to run your plugins on the Development Kit Raspberry ELK board, you need to setup your board, and transfer your plugin and configurations to it:

6. [ELK Development Board Setup.](07_elk_development_board_setup.md)

## 2. Tech Articles

On the MIND Music Labs website, there are a number of Tech articles that cover important topics directly relevant for learning to work with our Development Kit:

1. [How ELK Works: A look Under the Hood of a Device Powered By ELK.](https://www.mindmusiclabs.com/a-look-under-the-hood-of-a-device-powered-by-elk/)

2. [ELK Development Boards Overview.](https://www.mindmusiclabs.com/development-board-overview/)

3. [Discover SUSHI: The DAW Inside ELK.](https://www.mindmusiclabs.com/daw-like-sushi/)

4. [Controlling Plug-Ins in ELK, Part1: Open Sound Control.](https://www.mindmusiclabs.com/controlling-plug-ins-in-elk-part-1-open-sound-control/)

5. [Controlling Plug-Ins in ELK, Part2: Google Remote Procedure Call.](https://www.mindmusiclabs.com/controlling-plug-ins-in-elk-part-2-google-remote-procedure-call-grpc/)

We also present technical articles more broadly relevant for audio development:

1. [Audio Latency Demystified, Part 1.](https://www.mindmusiclabs.com/audio-latency-demystified-part-1/)

2. [Audio Latency Demystified, Part 2: Real-Time Linux Approaches.](https://www.mindmusiclabs.com/audio-latency-demystified-part-2-4-real-time-linux-approaches/)

3. [Using CMake for Compiling XMOS Code.](https://www.mindmusiclabs.com/using-cmake-for-compiling-xmos-code/)

4. [std::algorithm for Audio, Part 1.](https://www.mindmusiclabs.com/stdalgorithm-for-audio/)

5. [std::algorithm for Audio, Part 2.](https://www.mindmusiclabs.com/stdalgorithm-for-audio-part-ii/)

## 3. Examples

The Development Kit ships with a number of example plugins and configurations, which you should use to better learn what is possible with ELK:

1. MDA jx10 VST plugin example.
2. ...

## 4. ELK Online Forum

Finally, for any questions or discussion on the ELK Development Kit, please refer to the [ELK Online Forum](https://forum.elkmusicos.com). Currently forum participation is invitation only - please get in touch with us at MIND if you do not yet have an account!