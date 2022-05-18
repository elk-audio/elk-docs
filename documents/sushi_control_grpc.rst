.. _sushi-control-grpc:

Controlling Sushi using gRPC
============================

If you are unfamiliar with gRPC, reading our `article introducing gRPC in the context of Elk Audio OS <https://elk.audio/controlling-plug-ins-in-elk-part-ii/>`__
is a very good start.

We provide `up-to-date examples <elk_examples_overview.html>`__ of programs which use gRPC to communicate with Sushi.

All these examples are written in Python, and use our `elkpy Python library <https://github.com/elk-audio/elkpy>`__,
which greatly simplifies controlling Sushi over gRPC.

The elkpy library has comprehensive comments, and is also `documented here <../_modules/index.html>`__.

The heart of the gRPC integration, is the API definition in the
`sushi_rpc.proto file <https://github.com/elk-audio/sushi/blob/master/rpc_interface/protos/sushi_rpc.proto>`__, which you will find in the Sushi repository.

If you plan to work in Python we recommend using our elkpy library,
in which case you do not need to use gRPC directly, and can just read the documentation for elkpy.

But if you are programming a controller using a different language than Python,
referring to the above .proto file will be needed.
Even in that case, it will be very useful for you to read the comments for the elkpy methods,
and how the elkpy library invokes the Sushi gRPC API.

High-level Overview of Sushi's gRPC API
---------------------------------------

Given Sushi's complexity, we have split up the gRPC API to specific controllers, using each of which you can control one
subsystem of Sushi.

These are:

-  AudioGraphController
-  ParameterController
-  AudioRoutingController
-  MidiController
-  CvGateController
-  KeyboardController
-  ProgramController
-  OscController
-  TransportController
-  SystemController
-  TimingController
-  SessionController
-  NotificationController

All except NotificationController, define methods which the GUI can invoke to query Sushi on its state,
and to issue commands to modify that state.

For the details on the methods that can be invoked from each controller, please refer to the .proto file, and `elkpy library
documentation <../elkpy-doc/elkpy.html>`__.
Here we'll only present a high-level overview of what each is for, without too much detail of how it is used.

AudioGraphController
^^^^^^^^^^^^^^^^^^^^

With the AudioGraphController you can add/move/remove tracks and processors (plugins) at runtime,
and query the current tracks and plugins in the engine.

ParameterController
^^^^^^^^^^^^^^^^^^^

Allows the querying of all Track and Processor (plugin) parameters and properties, and setting them to new values.

AudioRoutingController
^^^^^^^^^^^^^^^^^^^^^^

Allows connecting audio input and output to and from tracks, and query the current connections.
When a track is added, no routings are automatically created, meaning you need to create them for it to process and output sound
to/from Sushi's inputs and outputs.

MidiController
^^^^^^^^^^^^^^

Similarly, with the MidiController you connect MIDI input and output to and from tracks, and query the current connections.
When a track is added, no MIDI connections are automatically created, meaning you need to create them for it to
process and output MIDI to/from Sushi's inputs and outputs.

CvGateController
^^^^^^^^^^^^^^^^

Enables the connection/disconnection of Control Voltage (CV) and Gate signals to and from processor parameters.
It also allows querying existing connections.

\* Note that in the current release version (0.11) of Sushi, while the .proto file API for creating and destroying is defined,
it is not yet functional.

KeyboardController
^^^^^^^^^^^^^^^^^^

KeyboardController allows you to send keyboard events to tracks - e.g. Note On/Off messages, pitch bend, modulation, etc.

ProgramController
^^^^^^^^^^^^^^^^^

For querying, loading and storing programs (pre-sets) for processors (plugins).

OscController
^^^^^^^^^^^^^

With the OscController you can query the state of the Open Sound Control broadcasting from Sushi, and selectively
enable/disable which parameters will broadcast their state. You can also fetch the IP and ports which Sushi uses for OSC.

TransportController
^^^^^^^^^^^^^^^^^^^

Enables querying and setting the state of the global Sushi transport - start/stop, time signature, tempo and tempo synchronisation methods.

SystemController
^^^^^^^^^^^^^^^^

Allows querying static information about the current Sushi version.

TimingController
^^^^^^^^^^^^^^^^

Allows the generation and querying of CPU timing information for the engine, individual tracks or processors.

SessionController
^^^^^^^^^^^^^^^^

Allows for saving and restoring the full state of Sushi including tracks, loaded plugins, audio and control routing.


Sushi gRPC Notifications
------------------------

NotificationController is different in that using it the GUI can subscribe to notifications of events happening in Sushi:
for example if a plugin is added/removed, a track is added/removed,
the transport state is changed, or a plugin's internal parameter has changed value.

\* Note that in the current release version (0.11) of Sushi, while the .proto file API is defined, transport and timing change
notifications are not yet enabled.
