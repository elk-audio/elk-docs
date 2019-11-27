.. Elk Audio OS documentation master file, created by
   sphinx-quickstart on Tue Nov 26 10:12:12 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Elk Development Kit Documentation
=================================

This collection of documents is our introduction and manual for the Elk
Development Kit.

The Development Kit consists of a software prototyping component, and
the Elk Add-On board for the Raspberry Pi/UpCore, which will be
documented here in that order.

You will be able to run the software component natively on a Debian
Linux, on a Virtual Machine, as well as on the Elk development board.
Mostly you interact with all three in a very similar way, but when there
are differences we will explicitly point them out.

.. toctree::
   :maxdepth: 2

   documents/elk_overview.md 

.. toctree::
   :maxdepth: 2
   :caption: Getting Started:

   documents/getting_started_with_development_kit_software.md
   documents/getting_started_with_development_kit_elk_pi_hardware.md
   documents/getting_started_with_development_kit_upcore_hardware.md
   documents/get_first_sound_from_devkit_board.md

.. toctree::
   :maxdepth: 2
   :caption: Elk Reference Manual:

   documents/devkit_further_topics.md
   documents/elk_examples_overview.md
   documents/sushi_configuration_format.md
   documents/sushi_internal_plugins.md
   documents/sensei_configuration_format.md
   documents/building_plugins_for_elk.md

Elk Online Forum
----------------

For any questions or discussion on the Elk Development Kit,
please refer to the `Elk Online Forum <https://forum.elk.audio>`__.

Tech Articles
--------------

On the Elk website, there are a number of Tech articles that cover
important topics directly relevant for learning to work with our
Development Kit:

   `How Elk Works: A look Under the Hood of a Device Powered By
   Elk <https://elk.audio/how-elk-works/>`__

   `Elk Development Boards
   Overview <https://elk.audio/elk-development-boards-overview-3/>`__

   `Discover Sushi: The DAW Inside
   Elk <https://elk.audio/daw-like-sushi/>`__

   `Controlling Plug-Ins in Elk, Part1: Open Sound
   Control <https://elk.audio/controlling-plug-ins-in-elk-part-i/>`__

   `Controlling Plug-Ins in Elk, Part2: Google Remote Procedure
   Call <https://elk.audio/controlling-plug-ins-in-elk-part-ii/>`__

Examples
--------

The Development Kit ships with a number of example plugins and
configurations, which you should use to better learn what is possible
with Elk:

   MDA JX10 VST2 plugin example

   MDA JX10 VST3 plugin example
   MDA JX10 LV2 plugin example

   Elk Multi FX Example

The examples can all be downloaded from our `GitHub elk-examples
repository <https://github.com/elk-audio/elk-examples/>`__.


Search Page
-----------

* :ref:`search`
