# Sushi Internal Plugins

These are simple integrated plugins that can be instantiated in a JSON config using the type "internal" and choosing one of the available options for the "uid" field.

Initially, the plugins under the namespace `sushi.testing.` have been developed mostly for internal purpose, but as of Sushi 1.1 and later there are a few that could be useful in some situations.

All parameters send-receive values normalized to the range 0.0-1.0. Their internal ranges and defaults are listed in parenthesis.

## Audio In / Audio Out Plugins

### Passthrough

Simply a bypass.

  * **uid** : "sushi.testing.passthrough"
  * **Parameters** : none

### Gain

Simple gain plugin. Note that the input controls are not smoothed, so it is not suitable to be used in a real-time context where the gain is adjusted by the user.
For those use cases, prefer using the internal gain parameter of the tracks.

  * **uid** : "sushi.testing.gain"
  * **Parameters** :
    + "gain" : gain in dB. Default: 0.83. (Normalized from [-120, 24], Default 0.0).

### Equalizer

Parametric peak equalizer (single band). The implementation is based on the warped bilinear transform following RBJ Audio Cookbook's formulas.
Parameters are smoothed and can be modulated at run-time.

  * **uid** : "sushi.testing.equalizer"
  * **Parameters** :
    + "frequency" : center frequency in Hertz. Default 0.05. (Normalized from [20, 20'000], Default: 1000.0).
    + "gain" : output gain in dB. Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "q" : Q factor of the filter. Default: 0.1. (Normalized from [0, 10], Default: 1.0).

### Mono Summing

Simple plugin that sums all input channels to mono, and outputs the same mono audio to all channels.

 * **uid** : "sushi.testing.mono_summing"

### Sample Delay

Simple delay plugin. Delays a track by unit of samples up to 48000 samples (one second at 48 kHz)

  * **uid** : "sushi.testing.sample_delay"
  * **Parameters** :
    + "sample_delay" : delay in samples. Default: 0.0. (Normalized from [0, 48000])

### Stereo Mixer

Utility plugin for mixing the two channels of a stereo signal independently.

  * **uid** : "sushi.testing.stereo_mixer"
  * **Parameters** :
    + "ch1_gain" : The gain of channel 1 (left channel) in dB (Normalized from [-120, 24], Default: 0.0)
    + "ch1_pan" : The pan of channel 1 (left channel) (Normalized from [-1.0, 1.0], Default: -1.0)
    + "ch1_invert_phase : Invert the phase of channel 1 (left channel) ([0.0, 1.0], Default: 0.0, set to > 0.5 to invert phase)
    + "ch2_gain" : The gain of channel 2 (right channel) in dB (Normalized from [-120, 24], Default: 0.0)
    + "ch2_pan" : The pan of channel 2 (right channel) (Normalized from [-1.0, 1.0], Default: -1.0)
    + "ch2_invert_phase : Invert the phase of channel 2 (right channel) ([0.0, 1.0], Default: 0.0, set to > 0.5 to invert phase)

### Send

Sends audio to a return bus plugin. Can be used for creating effect sends, parallel processing, or more complex audio routings. With the default parameters all channels of the track are sent to the return instance but it can also be configured to send a range of channels using the "channel_count" and "start_channel" parameters.

  * **uid** : "sushi.testing.send"
  * **Parameters** :
    + "gain" : Gain control of audio sent in dB. Default: 0.83. (Normalized from [-120, 24], Default 0 dB).
    + "channel_count" : The number of channels to send, Default: 1 (all channels). (Normalized from [0, 16], Default All).
    + "start_channel" : The channel number to start counting from. Default: 0. (Normalized from [0, 16], Default 0).
    + "dest_channel" : The destination channel on the return plugin for the first channel sent. If this is higher than the channel count of the track which host the return plugin, no audio is sent. Default: 0. (Normalized from [0, 16], Default 0).

  * **Properties**:
    + "destination_name" : Name of a Return plugin instance to send audio to.

### Return

Return bus plugin. Receives audio from 1 or several Send plugins. The audio output from a Return plugin is delayed by 1 processing buffer.

  * **uid** : "sushi.testing.return"
  * **Parameters** :

### Reverb (freeverb)

Wrapper around the famous freeverb public-domain implementation. Mono/stereo reverb based on a Schroeder/Moorer model with comb & allpass filters. The internal parameters sound best at 44.1 kHz and are usable also for 48 kHz, but not for higher sampling rates.

The implementation does not have any smoothing of parameter values, so if those are varied continuously some glitches could be heard. A workaround for a common situation consists in putting the reverb 100% wet in an auxiliary track with a send/return plugin combinatio (check `misc/config_files/freeverb_aux.json` for an example).

  * **uid** : "sushi.testing.freeverb"
  * **Parameters** :
    + "freeze" : Boolean switch - if set, the reverb contents are "frozen", useful to create drone effects. [0.0, 1.0], Default : 0 for off.
    + "dry" : Relative level of the direct output. [0.0, 1.0], Default : 1.0
    + "wet" : Relative level of the wet output. [0.0, 1.0], Default : 0.5
    + "room_size" : Model room size. [0.0, 1.0], Default : 0.5
    + "width" : Model width. [0.0, 1.0], Default : 0.5
    + "width" : Model high-frequency damping. [0.0, 1.0], Default : 0.5

## Midi In / Audio Out Plugins

### Sample Player

Simple polyphonic sample-based player. Only one sample can be loaded and played with pitch tracking and ADSR envelope.

  * **uid** : "sushi.testing.sampleplayer"
  * **Parameters** :
    + "volume" : static gain for the sample, in dB. Default: 0.77. (Normalized from [-120, 36], Default: 0.0).
    + "attack" : envelope attack time, in seconds. Default: 0.0. (Normalized from [0, 10], Default: 0.0).
    + "decay" : envelope decay time, in seconds. Default: 0.0. (Normalized from [0, 10], Default: 0.0).
    + "sustain" : envelope sustain level. Default: 1.0.
    + "release" : envelope release time, in seconds. Default: 0.0. (Normalized from [0, 10], Default: 0.0).

  * **Properties**:
    + "sample_file" : path to .wav file to load

## Midi In / Midi Out plugins

### Arpeggiator

Simple Arpeggiator that repeats in "UP" movement the held MIDI notes on the track, using Sushi's tempo configuration.

  * **uid** : "sushi.testing.arpeggiator"
  * **Parameters** :
    + "range" : octave range, as integer. Default: 0.25. (Normalized from: [1, 5], Default: 2).

### Transposer

Transposes incoming MIDI Note ON & OFF events by a fixed amount of semitones.

  * **uid** : "sushi.testing.transposer"
  * **Parameters** :
    + "transpose" : transpose amount in semitones. Default: 0.0. (Normalized from [-24, 24], Default: 0.0).

### Step Sequencer

A simple 8-step sequencer.

* **uid** : "sushi.testing.step_sequencer"
* **Parameters** :
  * For each of the 8 steps:
    + "pitch_1"..."pitch_8" : Pitch in semitones. Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "step_1"..."step_8" : Step on/off. Default: 1.0.
    + "step_ind_1"..."step_ind_8" (output only) : Indicator whether steps are turned on/off, to provide visual feedback when the sequencer is running. Default: 1.0.

## Audio In / Parameter Out plugins

### Peak Meter

Utility plugin that analyzes the level of the incoming audio signal and outputs peak signal levels and clip indications if the maximum value exceeds 1 (or -1 on the negative cycle) at a configurable rate. Supports metering of up to 16 channels on a track.

  * **uid** : "sushi.testing.peakmeter"
  * **Parameters (control)**:
    + "link_channels" : Links channels 0 and 1 in a stereo config, so that both will display the highest peak from any of them. Range: [0, 1], Default: 0, for off.
    + "only_peaks" : Send only peak values that are louder that the decaying value. For displays that handle peak decay by themselves, reduces the amount of updates sent. Range: [0, 1], Default: 0, for off.
    + "update_rate" : The rate at which peak updates are sent, in updates/s. Default: 1.0 (Normalized from [0.1, 25], Default: 25).
  * **Parameters (output only)**:
    + "level_n" : (n = 0 - 15) Detected peak level on channel n in dB. Normalized from [-120, 24].
    + "clip_n" : (n = 0 - 15) Clipped audio samples (abs > 1.0) in channel n. Range: [0, 1], Default: 0, for off.

## CV In / Out plugins

### CV to Control

Adapter plugin which converts CV/gate information to note on and note off messages, thus enabling CV/gate control of synthesizer plugins.

  * **uid** : "sushi.testing.cv_to_control"
  * **Parameters** :
    + "channel": MIDI channel. Default: 0.0. (Normalized from [0, 16], Default: 0.0).
    + "tune": Coarse tune parameter. Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "polyphony": Number of CV voices. Default: 0.0. (Normalized from [1, 4], Default: 1).
    + For each of the max possible CV voices:
      + "pitch_1"..."pitch_4": Pitch in semitones. Default: 0.0.
      + "velocity_1"..."velocity_4": Note On velocity. Default: 0.5.

### Control to CV

Adapter plugin to convert from note on and note off messages, to CV/gate information, enabling CV/gate control from MIDI plugins.

  * **uid** : "sushi.testing.control_to_cv"
  * **Parameters** :
    + "send_velocity": Switch velocity transmission on/off. Default off.
    + "send_modulation": Switch modulation transmission on/off. Default off.
    + "retrigger_enabled": Switch retrigger mode on/off. Default off.
    + "tune": Coarse tune parameter. Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "fine_tune": Fine tune parameter. Default: 0.5. (Normalized from [-1, 1], Default: 0.0).
    + "polyphony": Number of CV voices. Default: 0.0. (Normalized from [1, 4], Default: 1).
    + "modulation": Modulation parameter. Default: 0.5. (Normalized from [-1, 1], Default: 0.0).
    + For each of the max possible CV voices:
      + "pitch_1"..."pitch_4": Pitch in semitones. Default: 0.0.
      + "velocity_1"..."velocity_4": Note On velocity. Default: 0.5.

## Utility plugins

### Wav writer

This plugin passes through audio without altering it, while writing it to a wav file.

* **uid** : "sushi.testing.wav_writer"
* **Parameters**:
+ "recording" : Switch the plugin recording on/off. Range: [0, 1], Default: 0, for off.
+ "write_speed" : How often to write the audio data in seconds. Range: [0.5, 4.0], Default: 1.0

* **Properties**:
+ "destination_file" : The path and name of the file to write to. ".wav" is appended to the end of the property value internally.

### Wav Streamer

Plugin for streaming large audio files from disk.

* **uid** : "sushi.testing.wav_streamer"
* **Parameters**:
  + "volume" : Audio playback gain, in dB. Default: 0.79. (Normalized from [-90, 24], Default: 0.0).
  + "playback_speed" : The speed at which the audio file is played back. Range: [0.5, 2.0], Default: 1.0.
  + "fade_time" : The speed at which to fade in or out the audio when starting or stopping in seconds. Range: [0, 100.0], Default: 0.0.
  + "seek" : Sets the playback position relative to the start and end of the file. Range : [0, 1.0], Default: 0.0.
  + "playing" : Enables or disables audio playback. Range: [0.0, 1.0], Default : 0 for off.
  + "loop" : Enables or disables looped playback. Range: [0.0, 1.0], Default : 0 for off.
  + "playing" : Enables or disables exponential volume fades when starting or stopping. If disabled, volume fades linearily. Range: [0.0, 1.0], Default : 0 for off.

* **Parameters (output only)**:
  + "position" : Displays the current playback position relative to the start and end of the file. Range: [0, 1.0], Default: 0.0.
  + "length" : Displays the total length of the file in seconds. (Normalized from [0, 86400], Default: 0.0).

* **Properties**:
  + "file" : The path and name of the file to playback. If file loading fails, this property is updated with an error message.
