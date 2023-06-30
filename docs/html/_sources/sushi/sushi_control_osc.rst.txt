.. _sushi-control-osc:

Controlling Sushi using Open Sound Control
==========================================

Sushi supports `Open Sound Control
(OSC) <http://opensoundcontrol.org/introduction-osc>`__. And can both
send OSC updates and receive notes and parameter changes through OSC.

If you are unfamiliar with OSC, reading our `article introducing OSC in the context of Elk Audio OS <https://elk.audio/controlling-plug-ins-in-elk-part-i/>`__ is a very good start.

OSC Messages Sushi Reacts To
----------------------------

By default, Sushi listens on port 24024 for the following OSC commands:

+---------------------------+-----------+------------------------------------------+
| **Path**                  |**TypeTag**| **Arguments**                            |
+---------------------------+-----------+------------------------------------------+
| /parameter/plugin\_name/p | f         | parameter value                          |
| arameter\_name            |           |                                          |
+---------------------------+-----------+------------------------------------------+
| /parameter/plugin\_name/p | s         | property value                           |
| roperty\_name             |           |                                          |
+---------------------------+-----------+------------------------------------------+
| /bypass/plugin\_name      | i         | bypass state (1 = bypassed, 0 = enabled) |
+---------------------------+-----------+------------------------------------------+
| /keyboard\_event/track\_n\| siif      | event type ("note\_on", "note\_off",     |
| ame                       |           | "aftertouch"), channel, note index,      |
|                           |           | norm. value                              |
+---------------------------+-----------+------------------------------------------+
| /keyboard\_event/track\_n\| sif       | event type ("modulation", "pitch\_bend", |
| ame                       |           | "aftertouch"), channel, norm. value      |
+---------------------------+-----------+------------------------------------------+
| /program/plugin\_name     | i         | program id                               |
+---------------------------+-----------+------------------------------------------+
| /engine/set\_tempo        | f         | tempo in beats per minute                |
+---------------------------+-----------+------------------------------------------+
| /engine/set\_time\_signat\| ii        | time signature numerator, time signature |
| ure                       |           | denominator                              |
+---------------------------+-----------+------------------------------------------+
| /engine/set\_playing\_mod\| s         | "playing" or "stopped"                   |
| e                         |           |                                          |
+---------------------------+-----------+------------------------------------------+
| /engine/set\_sync\_mode   | s         | "internal", "ableton\_link" or "midi"    |
+---------------------------+-----------+------------------------------------------+
| /engine/set\_timing\_stat\| i         | 1 = enabled, 0 = disabled                |
| istics\_enabled           |           |                                          |
+---------------------------+-----------+------------------------------------------+
| /engine/reset\_timing\_st\| s(s)      | reset target ("all", "track",            |
| atistics                  |           | "processor"), track name/processor name  |
+---------------------------+-----------+------------------------------------------+

OSC Messages Sushi Transmits
----------------------------

By default, Sushi transmits to port 24023, on 127.0.0.1.

Besides reacting to OSC messages, Sushi can also echo some aspects of its state, by broadcasting OSC messages.
By default, this feature is disabled, but can be enabled either through adding a relevant section to the `json
configuration file <sushi_configuration_format.html>`__, or by invoking relevant commands over :ref:`gRPC<sushi-control-grpc>`.

The message transmitted are from plugins in Sushi, when internal parameter is changed - for example,
the level parameters of the internal Peak Meter plugin.

This works in two ways, either all, or only specific processors, are enabled. See the relevant section
in the documentation of the Sushi json configuration file for more details.

Changing the ports and IP address for sending and receiving OSC
---------------------------------------------------------------

The UDP port it receives from, and the port and IP it sends to, can be changed with
command-line arguments.

Just type *$ sushi --help* for the exact
syntax.

The IP and port values set can also be retrieved over the gRPC API.
