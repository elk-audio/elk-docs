# Build Plugins for Elk

Sushi is a headless Linux host that supports VST 2.x and 3.x plugins using an unmodified versions of Steinberg's APIs. It also supports LV2 plugins natively. So there is no need to do custom porting of your code, if it runs on a normal Linux system without dependencies on graphics libraries such as X11.

Depending on your current codebase, though, you might need some code changes to meet the above  mentioned requirements, and you will need to recompile your plugin using Elk's provided gcc-based cross-compiling toolchain. This document is a short guide to help you through the needed steps.

As a recommended first step, make sure that your plugin builds and runs under a normal Linux distribution, with a Linux plugin host such as Carla, MrsWatson or Sushi itself.

We provide a pre-configured Docker environment to help running all the cross-compilation tasks under both native Docker and Docker Desktop (for Windows and macOS), available at [ELK Audio OS builder](https://github.com/elk-audio/elk-audio-os-builder).

## Cross-Compiling Toolchain

For each targeted platform, we release a cross-compiling toolchain based on gcc (clang available on request) that replaces all the commands of the standard toolchain, including make, CMake, system libraries, etc.

You just need to source the environment setup script to activate the toolchain. From a terminal shell, enter the LinuxBuild directory with the Makefile in it and type:
```
$ source [path-to-extracted-sdk]/environment-setup-[aarch-name]-elk-linux
```

Then, proceed to build your plugin using e.g. make or CMake depending on your configuration. In case you need to build for other architectures, just replace the path to the environment script with the correct one depending on the installed cross-compiling toolchain.

If you had previously changed your  *LD_LIBRARY_PATH*, you would need to first issue the *#$ unset LD_LIBRARY_PATH* command, before activating the toolchain following the preceding instructions.  In case it is more convenient in your case, you can just remove the relevant line in */home/minddev/.zshrc*.

## VST 2.x Plugins Using Steinberg SDK

This use case is for those that don't rely on a cross platform framework like JUCE to generate VST 2.x plugins but rather want to use the original Steinberg SDK directly.

Please notice that Steinberg has discontinued the VST 2.4 SDK, so it is no longer available for download since October 2018. You are still allowed to use the SDK and release VST 2.4 plugins if you have obtained a signed agreement with Steinberg prior to that date.

The process in this case should be trivial if your plugin doesn't have dependencies on any graphical libraries - this includes the VST GUI framework.

## VST 3.x Plugins Using Steinberg SDK

This should also be straightforward by using standard build systems. Since release 3.6.6, Steinberg also uses CMake as its primary build system and since 3.6.10, there have been several improvements for making it easier to use in a cross-compiling setup.

There is an example plugin that can be compiled with the provided toolchain under *work/vst3-template*, that also includes examples for communication between RT and non-RT threads using atomic-based lockless FIFOs.

After cross-compilation, ensure that the folder under *"plugin.vst3/Contents"* is named **aarch64-linux**, not *arm64-linux*, or the plugin will not work on the Elk-Pi - rename it yourself if needed.

## VST Plugins Using JUCE

Most developers use a cross-platform framework like JUCE for generating multiple versions of their plugins for various OSes / formats.

JUCE actively supports Linux as a build target, so if you don't use any other third-party libraries incompatible with Linux, it should be straightforward to compile your plugins for Elk. 

Note that any caveats and issues applying to each specific plugin format (i.e. VST 2, 3 detailed above) apply also when these are exported using JUCE.

Moreover, there are a couple of important issues to consider:

### Plugins using JUCE Version 6 or above 

JUCE can be used directly for building VST plugins for Elk, due to its new "headless" features.

Observe that the build process still requires X11 headers to be available (and we have included these to our X-compilation SDK). The produced binary will then detect whether a display is available on the platform, and run as a headless plugin if not.

The [ELK Audio OS builder](https://github.com/elk-audio/elk-audio-os-builder) docker image contains a preinstalled SDK for Raspberry Pi 4 and JUCE 8 installation.

You can follow the instructions in the repository on how to install the docker image, run the container and build the AudioPlugin example.

For other JUCE-based projects, the process should be pretty straightfoward:

1. Make sure your JUCE project is using CMake and not Projucer for its build system

2. Set the output formats to `VST3`

3. Set this option in the JUCE Cmake command: ` VST3_AUTO_MANIFEST FALSE `


## LV2 Plugins

Due to the LV2 plugin format's architecture, it is often very straightforward to build LV2 plugins for Elk. [LV2 strongly encourages the complete separation of GUI and plugin core](https://lv2plug.in/book/#_simple_oscilloscope), meaning building a headless plugin can be as simple as specifying an argument to not include the GUI, when generating the build project.

To cross compile, the following steps are specific for cross-compiling the [Calf plugin collection](https://github.com/elk-community/calf), but should generalize well across the majority of LV2 plugins:

### Generate ttl files

This is done first since the script to generate the *.ttl* files won't run with the cross compilation toolchain.

1. Make a folder to put the native build in *mkdir native*.

2. Generate project files:

   ```
   $ ./autogen.sh --prefix=/absoute/path/to/calf/native
   ```

3. *make* and *make install* to generate the ttl files.

4. *make clean* to prepare for cross compilation.

### Cross Compile the plugins

1. Set up the cross-compilation toolchain:

   ```
   $ unset LD_LIBRARY_PATH
   $ source /path/to/environment-setup-cortexa7t2hf-neon-vfpv4-elk-linux-gnueabi
   ```

2. Create a directory to build and install the cross compilation files to *$ mkdir build*.

3. Generate the makefiles:

   ```
   $ ./configure --host=arm-elk-linux-gnu --prefix=/absolute/path/to/calf/build
   ```

4. Compile the plugin library:

   ```
   $ make
   $ make install
   ```

5. move the .ttl files to the cross-compiled build.

   ```
   $ cp native/lib/lv2/calf.lv2/*.ttl build/lib/lv2/calf.lv2/
   ```

6. move the folder *build/lib/calf.lv2* to the desired location on the Elk Pi. Make sure the path to the parent folder is in the *LV2_PATH* variable.

## Running Compiled Plugins with Sushi on a Virtual Machine

  1. Start JACK Server.
  2. Launch Sushi with a proper JSON file that has the path to your plugins. You can use one of the provided examples to start, e.g.:
      *sushi -j -c ~/work/sushi/example_configs/config_fx.json*
  3. Connect Sushi's input and output ports to the system.

On most hosts, VirtualBox offers a virtual audio interface with output only. If you need to test an effect plugin, a quick way is to load an audio file in Audacity, select the JACK audio engine and connect it to Sushi.

If you need to test MIDI, you can either use a Virtual MIDI Keyboard (like *vmpk*) or redirect a USB MIDI device to the virtual machine. In both cases, you can use *aconnect* to connect the MIDI port of your control device to Sushi's ALSA input ports, just as on a native machine.

## Running Compiled Plugins with Sushi on Elk Boards

Simply run sushi using the *-r* switch instead of the *-j* one used on a non Elk machine, e.g.:

   *sushi -r -c /path/to/your/config.json*

If you need to connect USB MIDI devices, just plug them and connect them with the *aconnect* tool.

To get a rough view of CPU performance, *top* and similar Linux tools will only show you the amount of CPU used in non-RT tasks. To see CPU usage of Real Time tasks, use:

   *watch -n 0.5 cat /proc/xenomai/sched/stat*

For a more fine-grained analysis, you can use Sushi's gRPC API to query timing statistics of each track and plugin.

## Analyzing and Resolving Xenomai Mode Switches

As in any real-time system, you are not allowed to call any operating system functions from your real-time processing callback. This includes among the others:

  * Any memory allocation, directly or by using e.g. standard library containers that can allocate on their own.
  * Any thread synchronization primitives, like mutexes, semaphores, etc.
  * Some operations that are usually RT-safe on desktop systems but are not on Xenomai, for example querying system timers with e.g. *clock_gettime*.

We provide the Twine library as a replacement for common use cases in audio plugins, e.g. querying timers and synchronizing worker threads in a real-time safe way.

Whenever you do an operation that is not RT-safe, Xenomai will perform a _mode switch_, i.e. it will give back control to the Linux Kernel to perform the needed system call and then will switch back to the Cobalt Kernel to continue its activity. The performance penalty for such operations is huge and you will very likely encounter audio dropouts if any mode switch will occur.

To see how many mode switches the Sushi process has encountered, run

```bash
$ evl ps -s 
```

The number of mode switches is indicated by the `ISW` counter.
This number should stay stable and it's especially important that it never grows at run-time after plugin initialization.

In order to spot code paths that are responsible for mode switches, you can launch Sushi under gdb and use a special command-line flag to activate the throw of SIGXCPU signal whenever a mode switch happens.

From a terminal in an Elk board, you need to:

  1. Launch Sushi inside gdb:
     *gdb sushi*
  2. Set gdb to break on SIGXCPU signal:
     *catch signal SIGXCPU*

     It is possible to use the *ignore* command in gdb to ignore the first N occurrences, in case you have some non-problematic mode switches at plugin initialization.

  2. Run by passing the *--debug-mode-sw* flag, plus any other argument in your case, e.g.:

    run -r --debug-mode-sw -c /path/to/your/config.json

When a mode switch occurs, you should now have control over the gdb shell, and be able to analyze the backtrace, local variables, etc. to find its cause.

As a separate tool that you can use in a virtual machine or your Linux development environment, Sushi also has a _dummy_ frontend that spawns a separate thread to mock the real-time process and never does any real-time calls. You can therefore use tools like *strace* or similar to spot OS calls on that thread. This is useful, for example, to spot timer related calls which can cause Xenomai lockups and are otherwise impossible to localize using the JACK frontend since the JACK audio server use the same calls on its own.

