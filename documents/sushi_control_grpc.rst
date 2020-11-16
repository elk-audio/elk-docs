.. _sushi-control-grpc:

Controlling Sushi using gRPC
============================

If you are unfamiliar with gRPC, reading our `article introducing gRPC in the context of Elk Audio OS <https://elk.audio/controlling-plug-ins-in-elk-part-ii/>`__ is a very good start.

We provide `up-to-date examples <elk_examples_overview.html>`__ of programs which use gRPC to communicate with Sushi.

All these examples are written in Python, and use our `elkpy Python library <https://github.com/elk-audio/elkpy>`__, which greatly simplifies controlling Sushi over gRPC.

The heart of the gRPC integration, is the API definition in the `sushi_rpc.proto file <https://github.com/elk-audio/sushi/blob/master/rpc_interface/protos/sushi_rpc.proto>`__, which you will find in the Sushi repository.

If you plan to work in Python we recommend using our elkpy library, in which case you do not need to use gRPC directly, and can just read the documentation for elkpy.

But if you are programming a controller using a different language than Python, referring to the above .proto file will be needed. Although even in that case,
reading how the elkpy library invokes the Sushi gRPC methods may be useful.

High-level Overview of Sushi's gRPC API
---------------------------------------

Given Sushi's complexity, we have split up the gRPC API to specific controllers, using each of which you can control one
subsystem of Sushi.

These are:

-  SystemController
-  KeyboardController
-  AudioGraphController
-  ProgramController
-  ParameterController
-  MidiController
-  AudioRoutingController
-  OscController
-  CvGateController
-  TransportController
-  TimingController
-  NotificationController

All except NotificationController, define methods which the GUI can invoke to query Sushi on its state,
and to issue commands to modify that state.

Sushi gRPC Notifications
------------------------

NotificationController is different in that using it the GUI can subscribe to notifications of events happening in Sushi:
for example if a plugin is added/removed, a track is added/removed, the transport state is changed, or a plugin's internal parameter has changed value.
