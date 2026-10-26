Embedded OS reference
=====================

The embedded distribution of Elk Audio OS is a custom Linux distribution that uses the `EVL (Xenomai 4) low-latency kernel <https://evlproject.org/>`_ and a set of optimized audio drivers on top of that.

This section covers information on how to use the embedded system, how to build audio plugins to run on it and, for the more advanced, how to directly customize the embedded distribution to your needs.


Elk Audio OS 1.4.0
==================

Elk Audio OS 1.4.0 for the Raspberry Pi 4 is based on the Yocto Project 6.0 LTS release (wrynose), with the EVL real-time core on Linux 6.18.

It ships Sushi 1.4.0, which adds a ZeroMQ IPC control API next to gRPC and OSC, and a new web UI to control Sushi from a browser. See :ref:`Sushi Web UI` for how to enable it.

Bluetooth audio streaming is now enabled by default.

Elk Audio OS 1.0
================

With ELK Audio OS v1.0 we switched from Xenomai 3 to Xenomai 4, also called EVL.

EVL is a major addition to the ELK Audio OS, and provides improved performance, scalability and ease of maintenance, across all supported platforms.

As part of the transition from Xenomai 3 to EVL, we no longer support the Elk Pi hardware, due to the legacy drivers it requires.
The ‘sensei’ application is also no longer included, as it too depended on legacy driver features.

.. toctree::
   :maxdepth: 2

   working_with_elk_board
   building_plugins_for_elk
   yocto_build

