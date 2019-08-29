# ELK Examples Overview

## MDA JX 10 VST 3 Example

This example contains the following files:

1. config_play_vst3.json - the SUSHI configuration file.
2. mda_jx10_vst3_open_stage_control_gui.json - the Open Stage Control GUI.
3. mda-vst3.vst3.tar.xz - a build of the MDA VST 3 plugins, containing also the JX 10 Synthesizer binary.

First unpack the tar-file with the plugin to your local drive, and edit the config_play_vst3.json to refer to that path.

It serves to demonstrate how to instantiate SUSHI with a single VST 3 instrument reacting to MIDI and OSC control messages, sent using the accompanying GUI file for Open Stage Control.

This is the example referred to also in our [Getting Started Guide](getting_started_with_development_kit.md).

## MDA JX 10 VST 2 Example

Control of plugin parameters is to certain extent different between VST 2 and 3, which we illustrate by including two examples involving the same synth plugin, JX 10, both for VST 2 and VST 3.

This example contains the following files:

1. config_play_vst2.json - the SUSHI configuration file.
2. mda_jx10_vst2_open_stage_control_gui.json - the Open Stage Control GUI.
3. mda-vst2-plugins.tar.gz - a build of the MDA VST 2 plugins, containing also the JX 10 Synthesizer binary.

The configuration file for this example differs to the one for the VST 3 version of the same plugin in one crucial way: besides routing MIDI messages directly to the plugin, it also:

1. Maps MIDI CC values to control synth parameters.
2. Connects MIDI Program Change messages to the plugin

You will also notice that the OSC GUI for the VST 2 version lacks controls for Pitch and Modulation, that the VST 3 version lacks.

The reason is, those are in VST 2 received and handled as MIDI values, from your MIDI controller keyboard, while, for the VST 3 version of the same plugin, they are control parameters exposed over the VST 3 standard.

The instructions for the VST 3 example in the [Getting Started Guide](getting_started_with_development_kit.md), translate straightforwardly also for this VST 2 example - just read VST2 where in the text and paths, VST3 is specified.

