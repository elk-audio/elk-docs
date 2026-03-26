.. _sushi-control-grpc:

Controlling Sushi using gRPC
============================

If you are unfamiliar with gRPC, reading our `article introducing gRPC in the context of Elk Audio OS <https://elk.audio/controlling-plug-ins-in-elk-part-ii/>`__
is a very good start.

We provide `up-to-date examples <elk_examples_overview.html>`__ of programs which use gRPC to communicate with Sushi.

All these examples are written in Python, and use our `elkpy Python library <https://github.com/elk-audio/elkpy>`__,
which greatly simplifies controlling Sushi over gRPC. The elkpy library has comprehensive comments, and is also `documented here <../_modules/index.html>`__.

For controlling Sushi from a C++ application we also provide a similiar wrapper library built in C++  `elkcpp library <https://github.com/elk-audio/elkcpp>`__

The heart of the gRPC integration, is the API definition in 
`sushi_rpc.proto file <https://github.com/elk-audio/sushi-grpc-api/protos/sushi_rpc.proto>`__.

One way to quickly interact directly with the gRPC API during development is to use an API tool that has support for gRPC, for
example [Postman](https://www.postman.com/). You can find more on how to load Sushi's .proto definition into Postman
[here](https://learning.postman.com/docs/sending-requests/grpc/grpc-request-interface/).

Though we recommend you to start with one of out wrapper libraries, elkpy or elkcpp, depending on your choice of programming language. This way you do not need to use gRPC directly, and can just read the documentation for the respective library.

High-level Overview of Sushi's gRPC API
---------------------------------------

Given Sushi's complexity, the gRPC API has been split into specific controllers, each of which controls one
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

All except NotificationController, define methods the GUI can invoke to query Sushi on its state, and issue commands to modify that state.

For the details on the methods that can be invoked from each controller, please refer to the .proto file, and `elkpy library
documentation <../elkpy-doc/elkpy.html>`__.
Here we'll only present a high-level overview of what each is for, without too much detail of how it is used.

AudioGraphController
^^^^^^^^^^^^^^^^^^^^

Allows adding/moving/removeing tracks and processors (plugins) at runtime and querying the current tracks and plugins in the engine.

ParameterController
^^^^^^^^^^^^^^^^^^^

Allows the querying of all Track and Processor (plugin) parameters and properties and setting them to new values.

AudioRoutingController
^^^^^^^^^^^^^^^^^^^^^^

Allows connecting audio input and output to and from tracks, and query the current connections. When a track is added, routings are not automatically created, but have to be explicitly connected in order to process and output sound to/from Sushi's inputs and outputs.

MidiController
^^^^^^^^^^^^^^

MidiController allows connecting MIDI input and output to and from tracks, mapping CC messages to parameter and querying the current connections.
When a track is added, MIDI connections are not automatically created, but have to be explicity added in order to process and output MIDI to/from Sushi's inputs and outputs.

CvGateController
^^^^^^^^^^^^^^^^

Enables the connection/disconnection of Control Voltage (CV) and Gate signals to and from processor parameters.
It also allows querying existing connections.

\* Note that in the current release version of Sushi, while the .proto file API for creating and destroying is defined,
it is not yet functional.

KeyboardController
^^^^^^^^^^^^^^^^^^

KeyboardController allows sending keyboard events to tracks - e.g. Note On/Off messages, pitch bend, modulation, etc.

ProgramController
^^^^^^^^^^^^^^^^^

For querying, loading and storing programs (pre-sets) for processors (plugins).

OscController
^^^^^^^^^^^^^

OscController allows querying the state of the Open Sound Control broadcasting from Sushi, and selectively
enable/disable which parameters will broadcast their state. Also used to fetch the IP and ports which Sushi uses for OSC.

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
^^^^^^^^^^^^^^^^^

Allows for saving and restoring the full state of Sushi including tracks, loaded plugins, audio and control routing.


Sushi gRPC Notifications
------------------------

NotificationController is different in that using it the GUI can subscribe to notifications of events happening in Sushi:
for example if a plugin is added/removed, a track is added/removed,
the transport state is changed, or a plugin's internal parameter has changed value.

