Embedded OS reference
=====================

The embedded distribution of Elk Audio OS is a custom Linux distribution that uses the `Xenomai project low-latency kernel <https://evlproject.org/>`_ and a set of optimized audio drivers on top of that.

This section covers information on how to use the embedded system, how to build audio plugins to run on it and, for the more advanced, how to directly customize the embedded distribution to your needs.


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

