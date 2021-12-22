.. _sushi-public-api-model:

Sushi Public API Model
======================

.. figure:: ./illustrations/sushi_architecture.png
   :alt: img

   *(Note that in the current 0.11 version of Sushi, return tracks are not yet implemented).*

Processors, Tracks, Plugins and Parameters
------------------------------------------

The central entity in Sushi is the Processor. Plugins, once loaded, are processors.
And, Tracks are also processors, with the difference,
that tracks can in turn contain a set of processors (originating as instantiated plugins).

Tracks, and the processors they contain, are all contained within the Audio Graph, through which all
modification of this structure is managed.

Each processor has:

-  A set of numerical parameters for realtime control
-  A set of properties containing string data
-  A set of input and output audio channels.
-  A set of input and output keyboard channels.
-  A set of programs (presets) which can be stored and recalled.

The audio and MIDI channels of a track need to be explicitly connected to the
inputs and outputs of Sushi.

The processors contained in tracks all have the same audio and keyboard connection counts, chained within the track.

Each audio track can be set to use one of three modes for the audio channel configuration:
“mono”, “stereo” and “multibus”. The multibus option is for tracks with several stereo output busses routable to different output.

Individually, the parameters of each processor can be controlled by various means:

-  Over MIDI Control Change.
-  Over Open Sound Control (OSC) messages.
-  Using CV connections.
-  Set directly over gRPC.
-  Set in the JSON configuration file to specific values on startup, or even played back over time during off-line processing.

Also, mappings can be created to each processor, to echo/notify of the change of state of its internal parameters:

-  Over OSC messages.
-  Using CV / Gate connections.
-  Using Notifications over gRPC.

Tracks have the following parameters that can be controller in realtime:

- "gain" : The volume of the main bus, in dB. Default: 0.83 (0dB). (Normalized from [-120, 24], Default: 0.0).
- "pan" : The left - right panning of the main bus. Default: 0.5. (Normalized from [-1, 1], Default: 0.0).
- "mute" : Mute the output of the track. Default: 0.0. (Normalized from [0, 1], Default: 0.0).

Multi-bus tracks have additional parameters to set the pan and gain of additional output busses:

- "gain_sub_n" : The volume of the n:th output bus, in dB. Default: 0.83 (0dB). (Normalized from [-120, 24], Default: 0.0).
- "pan_sub_n" : The left - right panning of the n:th output bus. Default: 0.5. (Normalized from [-1, 1], Default: 0.0).

Global Sushi Settings
---------------------

Besides parameter and property set per processor, there are several global settings to control in Sushi.

The set of physical audio, MIDI and CV/Gate channels to use (limited of course by the hardware Sushi runs on), are defined during startup,
and are not changeable run-time.

The Global Transport is used to set tempo, time signature and play/stop states, for any plugins that have internal sequencing,
and for Ableton Link and midi clock.

During run-time, timing data on the processor utilization can be generated and fetched / subscribed to.

Accessing the Sushi Model
-------------------------

For many of the above, there are ways of accessing the data over both the JSON configuration file, MIDI, OSC and gRPC.
For others, it may only be possible over one of the above.

Generally the complexity of what can be achieved with each is as follows:

-  `Configuration over JSON <sushi_configuration_format.html>`__: Everything that can be defined statically is possible to set in the configuration file.
-  The `gRPC API <sushi_control_grpc.html>`__: covers the vast majority of all that can be accessed, adding also run-time access for dynamic alterations and monitoring.
-  The `Open Sound Control API <sushi_control_osc.html>`__ defines a streamlined sub-set of operations, selected to make sense for the common contexts of use for OSC: Efficient messages notifying of parameter changes; and enabling End-Users to create their own custom GUI's and mappings to other media tools.
-  MIDI, CC and CV/Gate are also primarily defined to be accessible by end-users, for their integration of the audio device with their other technologies.

