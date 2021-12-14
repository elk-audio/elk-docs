# Sushi Configuration

Following is a reference for the Sushi .json configuration file.

## Main Host Configuration
**“host_config”**

* **“samplerate”** - The sample rate at which to run Sushi. This could change depending on the audio frontend, if the requested sample rate in not supported for instance.
* **"midi_inputs"** - The number of MIDI input ports to use. Defaults to 1.
* **"midi_outputs"** - The number of MIDI output ports to use. Defaults to 1.
* **"cv_inputs"** - The number of control voltage inputs to use.
* **"cv_outputs"** - The number of control voltage outputs to use.
* **“tempo”** - The default tempo in beats per minute.
* **“playing_mode”** - The default mode to start Sushi in. Affects sequencers and tempo synced plugins. Valid values are “playing” and “stopped”.
* **“tempo_sync”** - The mode of synchronising tempo. Valid modes are “internal” where Sushi is the master, “midi” for syncing to midi tempo, and “ableton_link” for synchronising with Ableton Link.
* **“time_signature”** - Time signature to use in Sushi.
    * **“numerator”** - Time signature numerator.
    * **“denominator”** - Time signature denominator.
* **“audio\_clip_detection”** - Sets whether audio inputs and/or outputs detect sample values outside of the range of the analog audio circuits.
    * **“inputs”** - Clip detection enabled on the audio inputs.
    * **“outputs”** - clip detection enabled on the audio outputs.
* **"master_limiter"** - Set to true to enable brickwall limiting on each engine output. Attack is 0 ms and Relase ~100ms
* **"portaudio_input_device"** - The index of the input audio device to use with PortAudio. Defaults to the system default.
* **"portaudio_output_device"** - The index of the output audio device to use with PortAudio. Defaults to the system default.
* **"rt_midi_input_mappings"** - List of RtMidi input mappings objects with the following members (only applies if built with RtMidi as midi frontend):
    * **"rt_midi_device"** - The index of the RtMidi port to connect to.
    * **"sushi_midi_port"** - The index of the sushi midi port to connect the device to.
    * **"virtual_port"** - True if the port should be a virtual midi port false if not. Default is false.
* **"rt_midi_output_mappings"** - List of RtMidi input mappings objects with the following members (only applies if built with RtMidi as midi frontend):
    * **"rt_midi_device"** - The index of the RtMidi port to connect to.
    * **"sushi_midi_port"** - The index of the sushi midi port to connect the device to.
    * **"virtual_port"** - True if the port should be a virtual midi port false if not. Default is false.

## Tracks
**“tracks”** : A json list of track objects with the following members:

* **“name”** - Name of the track, must be unique.
* **“mode”** - The channel mode of the track. Valid options are: “mono”, “stereo” and “multibus”. The last option is for tracks with several stereo output busses routable to different output.
* **“input_busses”** - The number of input busses on the track, where each bus is a stereo pair. Only valid if “mode” is set to “multibus”.
* **“output_busses”** - The number of output busses on the track, where each bus is a stereo pair. Only valid if “mode” is set to “multibus”.
* **“inputs”** - Audio inputs to the track. A list objects with the following members:
    * **“engine_bus”** - The id of the engine bus to route from, where each bus is a stereo pair: bus 0 represents channels 0 and 1, bus 1 channels 2 and 3, and so forth.
    * **“track_bus”** - The id of the track bus to route audio to.
    *   Alternatively:
    * **“engine_channel”** - The id of the engine input channel to route audio from.
    * **“track_channel”** - The id of the track channel to route audio to.
* **“outputs”** - Audio outputs from the track. A list objects with the following members:
    * **“engine_bus”** - The id of the engine bus to route to, where bus is a stereo pair where bus 0 represents channels 0 and 1, bus 1 channels 2 and 3, and so forth.
    * **“track_bus”** - The id of the track bus to route audio from.
    *   Alternatively:
    * **“engine_channel”** - The id of the engine output channel to route audio to.
    * **“track_channel”** - The id of the track channel to route audio from.
* **“plugins”** - A list of plugin objects that will be added to the track in the order specified here. Plugins can be either internal processors, VST 2.4 plugins, VST 3.6 plugins or LV2 plugins (natively, or using the LV2VST wrapper).
    * **“name”** - The name of the plugin - must be unique.
    * **“type”** - The plugin format, valid values are: “internal”, “vst2x”, “vst3x”, or "lv2" for native LV2 hosting.
    * **“path”** - The path to an external plugin. For VST 2, this is the path to the dynamic library, for VST 3 it is the path to the plugin directory. Not valid for internal plugins, or for natively hosting LV2.
    * **"uri"** - This is instead of "path", for natively hosting LV2 plugins. The LV2 standard requires that plugins are accessed through the LV2 platform tools, not directly. The paths where these platform tools search for plugins is on Linux defined with the LV2_PATH environment variable.
    * **“uid”** - For internal plugin this has the format “sushi.testing.plugin_name”. For VST 3 plugins, this is the string id of the plugin to load, as multiple plugins can be packaged inside one plugin folder/binary. Not valid for VST 2 plugins.

## MIDI
**“midi”** - MIDI connections to and from tracks and plugins.

* **“track_connections”** - A list of routing objects that route midi keyboard data to a track. MIDI keyboard data is routed to the first plugin on the track, that plugin then either consumes the MIDI data or passes it on to the next plugin on the track.
    * **“port”** - The integer id of the midi port to use as input.
    * **“channel”** - Only route MIDI data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“track”** - Name of the track to route MIDI to.
    * **“raw_midi”** - If set to true, Sushi will pass the raw MIDI events directly to the track without conversion
     or processing. If enabled, plugins will need to handle things like program change on their own. Default off. Only valid for plugin formats which accept raw MIDI data input, such as VST 2.
* **“track\_out\_connections”** - A list of routing objects for passing MIDI from a track to an external MIDI port.
    * **“port”** - The integer id of the MIDI port to output data to.
    * **“channel”** - Only route MIDI data with this particular channel. Valid options are integers between 0-15; if you need multiple channels, you can have multiple entries for each channel.
    * **“track”** - Name of the track to route MIDI from.
* **“cc_mappings”** - Map control change messages to parameters on plugins and tracks. A list of routing objects with the following members:
    * **“port”** - Integer id of the MIDI port to use as input.
    * **“channel”** - Only route MIDI data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“plugin_name”** - Name of the plugin.
    * **"cc_number"** - Index of incoming Control Change message to map.
    * **“parameter_name”** - Name of the parameter on the above plugin.
    * **“min_range”** -  A value between 0.0 and 1.0, defining what MIDI CC value 0 corresponds to when setting. (Setting parameters in Sushi and plugins can only be achieved using normalized floating-point values between 0.0-1.0).
    * **“max_range”** - A value between 0.0 and 1.0, defining what MIDI CC value 127 corresponds to when setting. (Setting parameters in Sushi and plugins can only be achieved using normalized floating-point values between 0.0-1.0).
    * **“mode”** - Mode for controller, one of "absolute" (default) or "relative".
* **“program\_change_connections”** - Route MIDI program change messages to a particular plugin. A list of routing objects with the following members:
    * **“port”** - Integer id of the MIDI port to use as input.
    * **“channel”** - Only route MIDI data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“plugin”** - Name of the plugin to route program changes to.

## Open Sound Control Connections

**"osc"** -  Open Sound Control (OSC) notifications from processors.

Here you can enable OSC messages to be transmitted from Sushi, whenever a processor's internal parameter is changed - for example, the level parameters of the internal Peak Meter plugin.

This works in two ways, either all, or only specific processors, are enabled. The below two all/processor entries are thus mutually exclusive, and cannot coexist in a configuration:

- **"enable_all_processor_outputs"** - Set this to true, for all possible OSC notifications, from all processors, to be transmitter.
- **"enable_processor_outputs"** - Enable the OSC notifications for the specific processors. This is a list of entries with fields:
  - **"processor"** - The name of the track or plugin to connect from.
    - **"parameter_blocklist"** - A list of parameters of the processor, which should not trigger notifications.
      - **"parameter"** - The name of the parameter to block the OSC output from.

## Control voltage and Gate connections

**"cv\_control"** - Control Voltage (CV) and Gate connections to and from tracks and plugins.

* **"cv\_inputs"** - Connect a CV input to parameter changes. Values from the selected CV input will be converted to parameter changes on the selected parameter. A list of routing objects with the following members:
    * **"cv"** - The id of the CV input to connect from.
    * **"processor"** - The name of the track or plugin to connect to.
    * **"parameter"** - The name of the parameter to connect the CV input to.
* **"cv\_outputs"** - Connect parameters to CV outputs Parameter changes from the plugin will appear as values on the selected CV output. A list of routing objects with the following members:
    * **"cv"** - The id of the CV input to send values to.
    * **"processor"** - The name of the track or plugin to connect from.
    * **"parameter"** - The name of the parameter to connect from.
* **"gate\_inputs"** - Connect gate inputs to plugins. A list of routing objects with the following members:
    * **"mode"** - The mode to use for the selected gate input, currently "note_event" is the only supported mode. In this mode gate events will be converted to note events and passed on to the plugin with the selected channel and note number (note on for positive gate and note off for negative).
    * **"processor"** - The name of the plugin or track to connect to.
    * **"gate"** - The id of the Gate input.
    * **"note\_no"** - The note number to use for gate events from this gate input (0-127)
    * **"channel"** - The note number to use for gate events from this gate input (0-15)
* **"gate\_outputs"** - Connect gate inputs to plugins. A list of routing objects with the following members:
    * **"mode"** - The mode to use for the selected gate output, currently "note_event" is the only supported mode. In this mode note events sent from the plugin with the selected note numbers and channel will be converted to gate events (note on to positive gate and note on) and sent to the selected Gate output.
    * **"processor"** - The name of the plugin to connect from.
    * **"gate"** - The id of the Gate output.
    * **"note\_no"** - The note number to use for gate events from this gate output (0-127)
    * **"channel"** - The note number to use for gate events from this gate output (0-15)

## Events
**“events”** - A list of events to play back during processing. For the offline and dummy frontends, these events will be played back at the time set in the event object. For Raspa and Jack frontends, the time member is ignored and all events are sent immediately when starting Sushi.

* **“type”** - Type of event, valid values are “parameter_change”, “property_change”, “note_on” and “note_off”.
* **“time”** - Time, in seconds from the start of Sushi, when event should be executed.
* **“data”** - Type specific data containing some of the following:
    * **“plugin_name”** - Name of the plugin or track to send the event to.
    * **“parameter_name”** - If “type” = “parameter_change”, the name of the parameter to set.
    * **“property_name”** - If “type” = “property_change”, the name of the string property to set.
    * **“value”** - If “type” = “parameter_change”, the decimal value to set. If “type” = “property_change”, the string value to set.
    * **“note”** - Note value to set, only valid if “type” = “note_on” or “note_off”.
    * **“velocity”** - Velocity value to set, only valid if “type” = “note_on” or “note_off”.
