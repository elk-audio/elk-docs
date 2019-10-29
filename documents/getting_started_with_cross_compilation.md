# Getting Started With Cross Compilation

## Overview

We take you through the steps to cross-compile our example JUCE plugin for Elk Pi.

As you may recall, plugins built for the Elk Pi platform need to be headless, i.e. without a GUI. For this purpose we have an Elk fork of JUCE, using which GUI dependencies are removed, so that a plugin can be cross-compiled without modifications to the plugin codebase itself.

Our JUCE example plugin consists of several components. For this guide, you need only:

1. **elk_juce_example_synth** - the plugin, which can play back a sample sound reacting to MIDI note input, and also has the JUCE internal Reverb effect embedded, with two parameters exposed, Room Size and Damping. The folder includes the code for the **Elk JUCE fork**, so that you can build without downloading it separately, and without building and running Projucer.
2. **elk_juce_plugin.json** - a Sushi configuration file, ready to use with the Elk Pi board.

To follow this guide you need to have access to the Steinberg VST2 2.4 SDK, which may be a problem if you do not already have it, since Steinberg no longer provides such licenses. This limitation is because JUCE at the time of writing still doesn't support exporting to VST3 for Linux. For this reason, the built binaries are also available to download from the elk-examples downloads section, for the Elk Pi, so that you can at least test all other steps of this guide, besides the actual compilation.

## Cross-Compilation:

Clone the [elk-examples bitbucket repository](https://bitbucket.org/mindswteam/elk-examples/src/master/) to your computer.

Under the folder `elk-examples/elk_juce_remote_plugin_example`, you will find the above items 1 and 2.

**Test-build the headless plugin for your Linux desktop computer:**

The project already includes a generated Makefile for Linux, and necessary modules of our Elk JUCE fork, so all you need for compiling is to point *make* to the right path for the VST 2.4 SDK.

1. Set reference to VST 2.4 SDK for plugin. Edit the Makefile  `/elk_juce_example_synth/Builds/LinuxMakeFile/Makefile`. In lines 38, and 68, change `-I$(HOME)/workspaces/vstsdk2.4`, to point to where on your computer you have the SDK folder.

2. Type

   ```bash
   $ make -j4 CFLAGS="-DJUCE_HEADLESS_PLUGIN_CLIENT=1"
   ```

   to build.

If you were to type only `$ make`, you would get errors because the X11 dependencies cannot be found, since the Elk fork removes them.

**Cross-compile it for the Elk Pi:**

**Download and install the cross-compilation toolset / connect to the remote AWS cross-compilation service.**

Type the following commands:

```bash
$ unset LD_LIBRARY_PATH
$ source /opt/elk-sika64-sdk/environment-setup-aarch64-elk-linux
```

To set the toolchain.

Then type the below to build:

```bash
$ AR=aarch64-elk-linux-gcc-ar make -j`nproc` CONFIG=Release CFLAGS="-DJUCE_HEADLESS_PLUGIN_CLIENT=1" TARGET_ARCH="-march=armv8-a -mtune=cortex-a72"
```

**Copy your new plugin to your Raspberry Pi and run it:**

Following a successful build, you need to copy the files `elk_juce_example_synth/Builds/LinuxMakefile/buils/Elk_JUCE_Example_Synth.so`, and `elk_juce_plugin.json` to your Elk Pi.

Follow the instructions in our guide [Start running Elk on Development Kit Hardware](get_first_sound_from_devkit_board.md) to connect to your board, if you have not already done so. 

Create the folder `/home/mind/plugins/juce-example` on the Raspberry Pi.

Assuming you have a network connection between your desktop computer and the Elk Pi, all you need to do is use the Secure Copy Protocol (SCP) to transfer each file.

On the command line that would be 

```bash
$ scp Elk_JUCE_Example_Synth.so mind@192.168.1.10:~/plugins/juce-example
```

and

```bash
$ scp elk_juce_plugin.so mind@192.168.1.10:~/config_files/
```

replacing `192.168.1.10`for your Elk Pi board's IP. You will be prompted to enter a password, the default being "elk".

Now, you can launch the plugin!

The steps for launching are detailed in the above mentioned guide, [Start running Elk on Development Kit Hardware](get_first_sound_from_devkit_board.md).

For more extensive documentation on cross-compilation, please refer to our reference manual section [Building Plugins for ELK](building_plugins_for_elk.md).