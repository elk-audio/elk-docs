# Sushi configuration file reference

## “host_config”
Main host configuration.

* **“samplerate”** - The sample rate at which to run Sushi. This could change depending on the audio frontend, if the requested sample rate in not supported for instance.
* **“tempo”** - The default tempo in beats per minute.
* **“playing_mode”** - The default mode to start Sushi in. Affects sequencers and tempo synced plugins. Valid values are “playing” and “stopped”.
* **“tempo_sync”** - The mode of synchronising tempo. Valid modes are “internal” where Sushi is the master, “midi” for syncing to midi tempo, and “ableton_link” for synchronising with Ableton Link.
* **“time_signature”** - Time signature to use in Sushi.
    * **“numerator”** - time signature numerator.
    * **“denominator”** - time signature denominator.
* **“audio\_clip_detection”** - sets whether audio inputs and/or outputs detects sample values outside of the range of the analog audio circuits.
    * **“inputs”** - clip detection enabled on the audio inputs.
    * **“outputs”** - clip detection enabled on the audio outputs.

## “tracks”
A json list of track objects with the following members:

* **“name”** - Name of the track, must be unique.
* **“mode”** - The channel mode of the track, valid options are: “mono”, “stereo” and “multibus”. The last option is for tracks with several stereo output busses routable to different output.
* **“input_busses”** - The number of input busses on the track, where each bus is a stereo pair. Only valid if “mode” is set to “multibus”.
* **“output_busses”** - The number of output busses on the track, where each bus is a stereo pair. Only valid if “mode” is set to “multibus”.
* **“inputs”** - Audio inputs to the track. A list objects with the following members:
    * **“engine_bus”** - The id of the engine bus to route from, where bus is a stereo pair where bus 0 represents channels 0 and 1, bus 1 channels 2 and 3…
    * **“track_bus”** - The id of the track bus to route audio to.
    *   Alternatively:
    * **“engine_channel”** - The id of the engine input channel to route audio from.
    * **“track_channel”** - The id of the track channel to route audio to.
* **“outputs”** - Audio outputs from the track. A list objects with the following members:
    * **“engine_bus”** - The id of the engine bus to route to, where bus is a stereo pair where bus 0 represents channels 0 and 1, bus 1 channels 2 and 3…
    * **“track_bus”** - The id of the track bus to route audio from.
    *   Alternatively:
    * **“engine_channel”** - The id of the engine output channel to route audio to.
    * **“track_channel”** - The id of the track channel to route audio from.
* **“plugins”** - A list of plugin objects that will be added to the track in the order specified here. Plugins can be either internal processors, Vst2.4 plugins, Vst3.6 plugins or LV2 plugins (under development).
    * **“name”** - The name of the plugin, must be unique.
    * **“type”** - The plugin format, valid values are: “internal”, “vst2x”, “vst3x”.
    * **“path”** - The path to an external plugin. For Vst 2, this is the path to the dynamic library, for Vst 3 it is the path to the plugin directory. Not valid for internal plugins.
    * **“uid”** - For internal plugin this has the format “sushi.testing.plugin_name”. For Vst 3 plugins, this is the string id of the plugin to load, as multiple plugins can be packaged inside one plugin folder/binary. Not valid for Vst 2 plugins.

## “midi”
Midi connections to and from tracks and plugins.

* **“track_connections”** - A list of routing objects that route midi keyboard data to a track. Midi keyboard data is routed to the first plugin on the track, that plugin then either consumes the midi data or passes it on to the next plugin on the track.
    * **“port”** - The integer id of the midi port to use as input.
    * **“channel”** - Only route midi data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“track”** - Name of the track to route midi to.
    * **“raw_midi”** - If set to true, sushi will pass the raw midi events directly to the track without conversion or processing. If enabled, plugins will need to handle things like program change on their own. Default off. only valid for plugin formats that accepts raw midi data as input like Vst 2.
* **“track\_out\_connections”** - A list of routing objects for passing midi from a track to an external midi port.
    * **“port”** - The integer id of the midi port to output data to.
    * **“channel”** - Only route midi data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“track”** - Name of the track to route midi from.
* **“cc_mappings”** - Map control change messages to parameters on plugins and tracks. A list of routing objects with the following members:
    * **“port”** - Integer id of the midi port to use as input.
    * **“channel”** - Only route midi data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“plugin_name”** - Name of the plugin.
    * **"cc_number"** - Index of incoming Control Change message to map
    * **“parameter_name”** - Name of the parameter on the above plugin.
    * **“min_range”** - Minimum normalized range of the mapping (in [0, 1]), what a value a cc value of 0 corresponds to.
    * **“max_range”** - Maximum normalized range of the mapping (in [0, 1]), corresponds to a cc value of 127.
    * **“mode”** - Mode for controller, one of "absolute" (default) or "relative"
* **“program\_change_connections”** - Route midi program change messages to a particular plugin. A list of routing objects with the following members:
    * **“port”** - Integer id of the midi port to use as input.
    * **“channel”** - Only route midi data with this particular channel. Valid options are integers 0-15 and “all”, which passes all channels.
    * **“plugin”** - Name of the plugin to route program changes to.

## “events”
A list of events to play back during processing. For the offline and dummy frontends, these events will be played back at the time set in the event object. For Raspa and Jack frontends, the time member is ignored and all events are sent immediately when starting Sushi.

* **“type”** - Type of event, valid values are “parameter_change”, “property_change”, “note_on” and “note_off”.
* **“time”** - Time, in seconds from the start of Sushi, when event should be executed.
* **“data”** - type specific data containing some of the following:
    * **“plugin_name”** - Name of the plugin or track to send the event to.
    * **“parameter_name”** - If “type” = “parameter_change”, the name of the parameter to set.
    * **“property_name”** - If “type” = “property_change”, the name of the string property to set.
    * **“value”** - If “type” = “parameter_change”, the decimal value to set. If “type” = “property_change”, the string value to set.
    * **“note”** - Note value to set, only valid if “type” = “note_on” or “note_off”.
    * **“velocity”** - Velocity value to set, only valid if “type” = “note_on” or “note_off”.
