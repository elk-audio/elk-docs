# Sushi Internal Plugins

These are simple integrated plugins that can be instantiated in a JSON config using the type "internal" and choosing one of the available options for the "uid" field.

Initially, the plugins under the namespace `sushi.testing.*` have been developed mostly for internal purpose, but as of Sushi 1.1 and later there are a few that could be useful in some situations. A more complete suite of production-level plugins is included through the [Brickworks library](https://www.orastron.com/brickworks) under the namespace `sushi.brickworks.*`.

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

## Brickwork plugins

From version 1.1, Sushi includes a suite of high-quality FX plugins written using the [Brickworks library](https://www.orastron.com/brickworks) by Orastron. They are mostly high-quality implementation of staple FXs (Chorus, distortions, etc.), with some lower-level plugins (e.g. multimode filter) that could be useful in some situations.

The implementation is especially good for parameter smoothing and coefficient interpolation, so they should all sound good even when the parameters are modulated at a high control frequency.

You can play around with all the FX plugins using the example configuration file `misc/config_files/play_brickworks_fx.json`, which sets all of them (bypassed by default) in a single track.

Brickworks is dual-licensed GPL3 / commercial; a commercial license of Sushi includes the rights to keep these plugins implementation inside it, but it does not give you the rights to extrapolate Brickworks and use it in another project unless you use the GPL3-licensed version of Brickworks.

Part of the following documentation is taken from the official Brickworks API.

### Bitcrusher

Bitcrusher through a combination of sample rate and bit-depth reduction

  * **uid** : "sushi.brickworks.bitcrusher"
  * **Parameters** :
    + "sr_ratio" : Sample rate reduction ratio. [0.0, 1.0], Default : 1.0
    + "bit_depth" : Relative level of the wet output. (Normalized from [1, 16], default : 16)

### Chorus

Chorus with variable rate and amount.

  * **uid** : "sushi.brickworks.chorus"
  * **Parameters** :
    + "rate" : Rate in Hz. (Normalized from [0.01, 2.0], default : 1.0)
    + "amount" : FX amount (0=none, 1=full). ([0.0, 1.0], default : 0.0)

### Clip

Antialiased hard clipper with parametric bias and gain (compensation) and output bias removal.

  * **uid** : "sushi.brickwork.clip"
  * **Parameters** :
    + "bias" : Input bias. (Normalized from [-2.5, 2.5], default : 0.0)
    + "gain" : Linear compensation output gain (Normalized from [0.1, 10.0], default : 1.0)

### Comb Delay

Comb filter / delay effect with feedforward and feedback paths.

  * **uid** : "sushi.brickwork.comb_delay"
  * **Parameters** :
    + "ff_delay" : Feedforward delay time in seconds. (Normalized from [0.0, 1.0], default : 0.05)
    + "fb_delay" : Feedback delay time in seconds. (Normalized from [0.0, 1.0], default : 0.05)
    + "blend" : Blend coefficient ([0.1, 10.0], default : 1.0)
    + "ff_coeff" : Feedfoward coefficient (Normalized from [-1.0, 1.0], default : 0.0)
    + "fb_coeff" : Feedback coefficient (Normalized from [-0.995, 0.995], default : 0.0)

### Compressor

Feedfoward compressor/limiter. In a multichannel track, the control signal is obtained with a -3dB sum of all the input channels.

  * **uid** : "sushi.brickwork.compressor"
  * **Parameters** :
    + "threshold" : Compression threshold in dB. (Normalized from [-60, 12], default : 0.0)
    + "ratio" : Compression ratio; the value is actually the slope of the gain curve above the threshold, hence `1.0` means no compression and `0.0` is a hard limit. ([0.0, 1.0], default: 1.0)
    + "attack" : Attack time constant in seconds. ([0.0, 1.0], default : 0.0)
    + "release" : Release time constant in seconds. ([0.0, 1.0], default : 0.0)
    + "gain" : Output makeup gain in dB. (Normalized from [-60, 60], default : 0.0)
    
### Distortion

Distortion effect, loosely inspired to the "rodent" distortion pedal.

  * **uid** : "sushi.brickwork.dist"
  * **Parameters** :
    + "dist" : Distortion (input gain, approximately). ([0.0, 1.0], default : 0.0)
    + "tone" : Tone (filter) parameter. ([0.0, 1.0], default : 0.5)
    + "volume" : Volume (output gain) ([0.0, 1.0], default : 1.0)

### Drive

Overdrive effect, loosely inspired to the green "screaming" overdrive pedal.

  * **uid** : "sushi.brickwork.drive"
  * **Parameters** :
    + "drive" : Overdrive (input gain, approximately). ([0.0, 1.0], default : 0.0)
    + "tone" : Tone (filter) parameter. ([0.0, 1.0], default : 0.5)
    + "volume" : Volume (output gain) ([0.0, 1.0], default : 1.0)

### 3-band Equalizer

Equalizer with 2nd-order (12 dB/oct) low shelf, peak and high shelf filters, similar to the one found in most channel strips.

  * **uid** : "sushi.brickwork.eq3band"
  * **Parameters** :
    + "lowshelf_freq" : Low shelf cutoff frequency in Hz. (Normalized from [25.0, 1'000.0], default : 125.0)
    + "lowshelf_gain" : Low shelf gain in dB. (Normalized from [-24, 24.0], default : 0.0)
    + "lowshelf_q" : Low shelf Q factor. (Normalized from [0.5, 5.0], default : 1.0)
    + "peak_freq" : Peak cutoff frequency in Hz. (Normalized from [25.0, 20'000.0], default : 1'000.0)
    + "peak_gain" : Peak gain in dB. (Normalized from [-24, 24.0], default : 0.0)
    + "peak_q" : Peak Q factor. (Normalized from [0.5, 5.0], default : 1.0)
    + "highshelf_freq" : High shelf cutoff frequency in Hz. (Normalized from [1'000.0, 20'000.0], default : 4'000.0)
    + "highshelf_gain" : High shelf gain in dB. (Normalized from [-24, 24.0], default : 0.0)
    + "highshelf_q" : High shelf Q factor. (Normalized from [0.5, 5.0], default : 1.0)

### Flanger

Flanger with variable rate and amount.

  * **uid** : "sushi.brickworks.flanger"
  * **Parameters** :
    + "rate" : Rate in Hz. (Normalized from [0.01, 2.0], default : 1.0)
    + "amount" : FX amount (0=none, 1=full). ([0.0, 1.0], default : 0.0)

### Fuzz

Fuzz effect, loosely inspired to the "smiling" fuzz pedal.

  * **uid** : "sushi.brickwork.fuzz"
  * **Parameters** :
    + "fuzz" : Fuzz (input gain, approximately). ([0.0, 1.0], default : 0.0)
    + "volume" : Volume (output gain) ([0.0, 1.0], default : 1.0)

### Highpass

First-order highpass filter (6 dB/oct) with gain asymptotically approaching unity as frequency increases.

  * **uid** : "sushi.brickwork.highpass"
  * **Parameters** :
    + "frequency" : Cutoff frequency in Hz. (Normalized from [20.0, 20'000.0], default : 50.0)

### Multifilter

Second-order multimode filter, the various modes are blended together with a set of coefficient parameters.

  * **uid** : "sushi.brickwork.multi_filter"
  * **Parameters** :
    + "frequency" : Cutoff frequency in Hz. (Normalized from [20.0, 20'000.0], default : 1'000.0)
    + "Q" : Q factor. (Normalized from [0.5, 10.0], default : 1.0)
    + "input_coeff" : Input mode coefficient. (Normalized from [-1.0, 1.0], default : 1.0)
    + "lowpass_coeff" : Lowpass mode coefficient. (Normalized from [-1.0, 1.0], default : 0.0)
    + "bandpass_coeff" : Bandpass mode coefficient. (Normalized from [-1.0, 1.0], default : 0.0)
    + "highpass_coeff" : Highpass mode coefficient. (Normalized from [-1.0, 1.0], default : 0.0)

### Noise gate

Noise gate; in case of multichannel setup, each channel is used independently for the control signal of that channel's gate.

  * **uid** : "sushi.brickwork.noise_gate"
  * **Parameters** :
    + "threshold" : Threshold in dB. (Normalized from [-60.0, 60.0], default : 0.0)
    + "ratio" : Compression ratio (0 = no gating, 1 = hard gate). ([0.0, 1.0], default: 0.0) 
    + "attack" : Attack time constant in seconds. ([0.0, 1.0], default : 0.0)
    + "release" : Release time constant in seconds. ([0.0, 1.0], default : 0.0)

### Notch

Second-order notch filter with unitary gain at DC and asymptotically as frequency increases, and null gain at cutoff frequency.

  * **uid** : "sushi.brickwork.notch"
  * **Parameters** :
    + "frequency" : Center frequency in Hz. (Normalized from [20.0, 20'000.0], default : 1'000.0)
    + "Q" : Q factor. (Normalized from [0.5, 10.0], default : 1.0)

### Phaser

Phaser containing 4 1st-order allpass filters modulated by a sinusoidal LFO.

  * **uid** : "sushi.brickworks.phaser"
  * **Parameters** :
    + "rate" : Modulation rate in Hz. (Normalized from [0.5, 5.0], default : 1.0)
    + "center" : Center frequency in Hz. (Normalized from [100, 10'000.0], default : 1'000.0)
    + "amount" : Modulation amount in octaves. (Normalized from [0.0, 4.0], default : 1.0)

### Saturation

Antialiased tanh-based saturation with parametric bias and gain (compensation) and output bias removal.

  * **uid** : "sushi.brickwork.saturation"
  * **Parameters** :
    + "bias" : Input bias. (Normalized from [-2.5, 2.5], default : 0.0)
    + "gain" : Input gain (Normalized from [0.1, 10.0], default : 1.0)

### Tremolo

Tremolo with variable speed and amount.

  * **uid** : "sushi.brickworks.tremolo"
  * **Parameters** :
    + "rate" : Modulation rate in Hz. (Normalized from [1.0, 20.0], default : 1.0)
    + "amount" : Modulation amount (0 = no tremolo, 1 = full tremolo). ([0.0, 1.0], default : 1.0)

### Vibrato

Tremolo with variable speed and amount.

  * **uid** : "sushi.brickworks.vibrato"
  * **Parameters** :
    + "rate" : Rate in Hz. (Normalized from [2.0, 10.0], default : 4.0)
    + "amount" : FX amount (0=none, 1=full). ([0.0, 1.0], default : 0.0)

### Wah

Simple wah digital effect.

  * **uid** : "sushi.brickworks.vibrato"
  * **Parameters** :
    + "wah" : Wah pedal position (0 = low cutoff, 1 = high cutoff) ([0.0, 1.0], default : 0.5)

### Simple synthesizer

Very simple monophonic synthesizer consisting of a single PWM oscillator, a state variable lowpass filter and ADSR envelope.

  * **uid** : "sushi.brickworks.simple_synth"
  * **Parameters** :
    + "volume" : Output volume in dB (Normalized from [-60.0, 12.0], default : 0.0)
    + "portamento" : Portamento time in seconds. ([0.0, 1.0], default : 0.01)
    + "pulse_width" : Pulse width ([0.0, 1.0], default : 0.5)
    + "filter_cutoff" : Filter cutoff in Hz (Normalized from [20.0, 20'000.0], default : 4'000.0)
    + "filter_Q" :  Filter Q factor / resonance. (Normalized from [0.5, 10.0], default : 1.0)
    + "attack" : Attack time constant in seconds. ([0.0, 1.0], default : 0.01)
    + "decay" : Decay time constant in seconds. ([0.0, 1.0], default : 0.01)
    + "sustain" : Sustain level. ([0.0, 1.0], default : 1.0)
    + "release" : Release time constant in seconds. ([0.0, 1.0], default : 0.01)

