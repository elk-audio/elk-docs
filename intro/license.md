# Elk Audio OS software licenses

Elk is a Linux distribution, containing many different software packages and libraries.

The core Elk components, i.e. what makes Elk different than a generic embedded Linux distribution, are **dual-licensed**, with a copyleft open-source license available for free to everyone and a proprietary commercial license available on request.

The other components are redistributed following their original license's terms.

## Open-source license

The open-source license comes completely free of charge and is the best way to get started. It is a "copyleft" license, which means that if you want to redistribute a product using it, you will also have to open-source your own code.

Binaries and support are provided only for the Raspberry Pi 4, for which images are available [on Github](https://github.com/elk-audio/elk-pi).

The specific licenses for the Elk components are detailed in each Github repository:

  * [Sushi](https://github.com/elk-audio/sushi) and [Sensei](https://github.com/elk-audio/sensei) are released under the Affero GPL 3.0
  * [TWINE](https://github.com/elk-audio/twine), [RASPA](https://github.com/elk-audio/raspa), [elkpy](https://github.com/elk-audio/elkpy) and [elkcpp](https://github.com/elk-audio/sushi) are released under the GPL 3.0
  * All the kernel components, including the [real-time audio driver](https://github.com/elk-audio/https://github.com/elk-audio/rpi-rtdm-audio-driver) are released under the GPL 2.0
  * The [Yocto/Openembedded configuration](https://github.com/elk-audio/elkpi-yocto-layers) is released under the MIT 2.0 license

Since Sushi & Sensei are licensed with the Affero variant, any external software (either running in the embedded system or in another machine) will be affected and therefore you will have to provide source code for it if you choose this license. This also applies to any plugin hosted by Sushi.

The open-source version of Elk Audio OS comes with a best effort community support through our [forum community](https://forum.elk.audio/).

## Commercial license

If you do not want to release your own source code, or if you want to run Elk on architectures other than the Raspberry Pi, commercial licenses are available.

For pricing and more details on commercial licenses, please contact us at info@elk.audio

