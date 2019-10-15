# ELK Examples Overview

All example files can all be downloaded from our [private elk-examples bitbucket repository](https://bitbucket.org/mindswteam/elk-examples/src/master/), with the binary files they depend on available in [that repository's Downloads section](https://bitbucket.org/mindswteam/elk-examples/downloads/).

For the first three examples, on hosting and controlling a plugin, we have chosen to use the same instrument, the [MDA JX 10 synthesizer](https://sourceforge.net/projects/mda-vst/), using ports of it for all three different plugin formats. This allows us to better illustrate the slight differences between the formats, than if we had used a different plugin for each.

Note that the linked files below, are all built for running natively on Linux, on the provided Virtual Machine image, or the UpCode board - all of which are for an Intel architecture.

Plugin binaries built to run natively on the Rasberry Pi, will be provided in separate links.

## MDA JX 10 VST 3 Example

This example requires the following files:

1. config_play_vst3.json - the Sushi configuration file.
2. mda_jx10_vst3_open_stage_control_gui.json - the Open Stage Control GUI.
3. [mda-vst3.vst3.tar.xz](https://bitbucket.org/mindswteam/elk-examples/downloads/mda-vst3.vst3.tar.xz) - a build of the MDA VST 3 plugins, containing also the JX 10 Synthesizer binary.
4. mda-vst3-touchosc-gui.touchosc - a [TouchOSC](https://hexler.net/products/touchosc) GUI controller, mirroring the controls in the Open Stage Control GUI.

First unpack the tar-file with the plugin to your local drive, and edit the config_play_vst3.json to refer to that path.

It serves to demonstrate how to instantiate Sushi with a single VST 3 instrument reacting to MIDI and OSC control messages, sent using the accompanying GUI file for Open Stage Control.

This is the example referred to also in our [Getting Started Guide](getting_started_with_development_kit.md).

## MDA JX 10 VST 2 Example

Control of plugin parameters is to certain extent different between VST 2 and 3, which we illustrate by including two examples involving the same synth plugin, JX 10, both for VST 2 and VST 3.

This example requires the following files:

1. config_play_vst2.json - the Sushi configuration file.
2. mda_jx10_vst2_open_stage_control_gui.json - the Open Stage Control GUI.
3. [mda-vst2-plugins.tar.gz](https://bitbucket.org/mindswteam/elk-examples/downloads/mda-vst2-plugins.tar.gz) - a build of the MDA VST 2 plugins, containing also the JX 10 Synthesizer binary.

The configuration file for this example differs to the one for the VST 3 version of the same plugin in one crucial way: besides routing MIDI messages directly to the plugin, it also:

1. Maps MIDI CC values to control synth parameters.
2. Connects MIDI Program Change messages to the plugin

You will also notice that the OSC GUI for the VST 2 version lacks controls for Pitch and Modulation, which the VST 3 version has.

The reason is, those are in VST 2 received and handled as MIDI values, from your MIDI controller keyboard, while, for the VST 3 version of the same plugin, they are control parameters exposed over the VST 3 standard.

The instructions for the VST 3 example in the [Getting Started Guide](getting_started_with_development_kit.md), translate straightforwardly also for this VST 2 example - just read VST2 where in the text and paths, VST3 is specified.

## LV2VST Wrapper, with MDA JX 10 LV2 Example

This example requires the following files:

1. config_play_lv2vst.json - the Sushi configuration file.
2. mda_jx10_lv2vst_open_stage_control_gui.json - the Open Stage Control GUI.
3. [lv2vst_plugin.tar.xz](https://bitbucket.org/mindswteam/elk-examples/downloads/lv2vst_plugin.tar.xz) - the lv2vst plugin, and it's corresponding .whitelist file, referring it to which LV2 plugin it should load.
4. [mod-mda-JX10.lv2.tar.xz](https://bitbucket.org/mindswteam/elk-examples/downloads/mod-mda-JX10.lv2.tar.xz) - the LV2 MDA JX 10 plugin.

Again, the instructions for the VST 3 example in the [Getting Started Guide](getting_started_with_development_kit.md), translate straightforwardly also for this example.

The extra preparation step is needed however, to extract the `mod-mda-JX10.lv2.tar.xz` file content to /usr/lib/lv2/mod-mda-JX10.lv2. This is one of the paths where LV2 plugins should be installed, according to the LV2 standards conventions.

## ELK Multi FX Example

This example requires the following files:

1. elk_multi_fx.json - the Sushi configuration file.
2. elk_multi_fx_open_stage_control_gui.json - the Open Stage Control GUI.
3. elk_multi_fx_main_app - a python script that acts as a middleman, a 'glue' app, between Sushi and the Open stage control GUI.
4. [mda-vst2-plugins.tar.gz](https://bitbucket.org/mindswteam/elk-examples/downloads/mda-vst2-plugins.tar.gz) - a build of the MDA VST 2 plugins, containing the effects the example invokes.

This is a more complex example, demonstrating a simple 'glue' app between Sushi and an external hardware controller or software GUI, to enable 2-way communication between the controller and Sushi, as well as setting parameter values and programs over gRPC. An Open Stage Control setup is provided, but the OSC paths are deliberately named so as to mimic those of a hardware control surface set up with Sensei.

The example consists of 4 simple effect plugins in series, each having 1 adjustable parameter, an enabled/disabled button with indicator and 4 preset slots.
