# Sushi Internal Plugins

These are simple integrated plugins that can be instantiated in a JSON config using the type "internal" and choosing one of the available options for the "uid" field.

They have been developed mostly for internal testing purpose but you could find a use for them in some situations.

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
    + "gain" : gain in dB. Range: [0, 1], Default: 0.83. (Normalized from [-120, 24], Default 0.0).

### Equalizer

Parametric peak equalizer (single band). The implementation is based on the warped bilinear transform following RBJ Audio Cookbook's formulas.
Parameters are smoothed and can be modulated at run-time.

  * **uid** : "sushi.testing.equalizer"
  * **Parameters** :
    + "frequency" : center frequency in Hertz. Range: [0, 1], Default 0.05. (Normalized from [20, 20'000], Default: 1000.0).
    + "gain" : output gain in dB. Range: [0, 1], Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "q" : Q factor of the filter. Range: [0, 1], Default: 0.1. (Normalized from [0, 10], Default: 1.0).

## Midi In / Audio Out Plugins

### Sample Player

Simple polyphonic sample-based player. Only one sample can be loaded and played with pitch tracking and ADSR envelope.

  * **uid** : "sushi.testing.sampleplayer"
  * **Parameters** :
    + "volume" : static gain for the sample, in dB. Range: [0, 1], Default: 0.77. (Normalized from [-120, 36], Default: 0.0).
    + "attack" : envelope attack time, in seconds. Range: [0, 1], Default: 0.0. (Normalized from [0, 10], Default: 0.0).
    + "decay" : envelope decay time, in seconds. Range: [0, 1], Default: 0.0. (Normalized from [0, 10], Default: 0.0).
    + "sustain" : envelope sustain level. Range: [0, 1], Default: 1.0
    + "release" : envelope release time, in seconds. Range: [0, 1], Default: 0.0. (Normalized from [0, 10], Default: 0.0).

  * **Properties**:
    + "sample_file" : path to .wav file to load

## Midi In / Midi Out plugins

### Arpeggiator

Simple Arpeggiator that repeats in "UP" movement the held MIDI notes on the track, using Sushi's tempo configuration.

  * **uid** : "sushi.testing.arpeggiator"
  * **Parameters** :
    + "range" : octave range, as integer. Range: [0, 1], Default: 0.25. (Normalized from: [1, 5], Default: 2).

### Transposer

Transposes incoming MIDI Note ON & OFF events by a fixed amount of semitones.

  * **uid** : "sushi.testing.transposer"
  * **Parameters** :
    + "transpose" : transpose amount in semitones. Range: [0, 1], Default: 0.0. (Normalized from [-24, 24], Default: 0.0).

## Audio In / Parameter Out plugins

### Peak Meter

Basic plugin that analyzes the level of the incoming audio signal at 25 Hz rate and outputs parameter values corresponding to the detected level.

  * **uid** : "sushi.testing.peakmeter"
  * Parameters (output only) :
    + "left" : detected level on the left track in dB. Range: [0, 1], normalized from [-120, 0].
    + "right" : detected level on the right track in dB. Range: [0, 1], normalized from [-120, 0].

## CV In / Out plugins

### CV to Control

Adapter plugin which converts CV/gate information to note on and note off messages, thus enabling CV/gate control of synthesizer plugins.

  * **uid** : "sushi.testing.cv_to_control"
  * **Parameters** :
    + "channel": MIDI channel. Range: [0, 1], Default: 0.0. (Normalized from [0, 16], Default: 0.0).
    + "tune": Coarse tune parameter.  Range: [0, 1], Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "polyphony": Number of CV voices.  Range: [0, 1], Default: 0.0. (Normalized from [1, 4], Default: 1).
    + For each of the max possible CV voices:
      + "pitch_1"..."pitch_4": Pitch in semitones. Range: [0, 1], Default: 0.0.
      + "velocity_1"..."velocity_4": Note On velocity. Range: [0, 1], Default: 0.5.

### Control to CV

Adapter plugin to convert from note on and note off messages, to CV/gate information, enabling CV/gate control from MIDI plugins.

  * **uid** : "sushi.testing.control_to_cv"
  * **Parameters** :
    + "send_velocity": Switch velocity transmission on/off. Default off.
    + "send_modulation": Switch modulation transmission on/off. Default off.
    + "retrigger_enabled": Switch retrigger mode on/off. Default off.
    + "tune": Coarse tune parameter.  Range: [0, 1], Default: 0.0. (Normalized from [-24, 24], Default: 0.0).
    + "fine_tune": Fine tune parameter.  Range: [0, 1], Default: 0.5. (Normalized from [-1, 1], Default: 0.0).
    + "polyphony": Number of CV voices.  Range: [0, 1], Default: 0.0. (Normalized from [1, 4], Default: 1).
    + "modulation": Modulation parameter.  Range: [0, 1], Default: 0.5. (Normalized from [-1, 1], Default: 0.0).
    + For each of the max possible CV voices:
      + "pitch_1"..."pitch_4": Pitch in semitones. Range: [0, 1], Default: 0.0.
      + "velocity_1"..."velocity_4": Note On velocity. Range: [0, 1], Default: 0.5.