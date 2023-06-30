# Building an Elk Audio OS image using Yocto

We use tools from the [Yocto project](https://www.yoctoproject.org/) to generate all the embedded Linux distribution artifacts.

All the Yocto configuration needed for making an Elk image is available on Github, and with that you can generate for example:

  1. Your own customized image for target hardware such as RPi4
  2. Cross-compiling SDK
  3. Other types of embedded images and/or native programming tools

With ELK audio OS v1.0 we simplified Yocto build process by using the [KAS tool](https://github.com/siemens/kas)

In order to simplify the whole image build starting from source code we also provide:

1. [ELK Audio OS builder](https://github.com/elk-audio/elk-audio-os-builder) docker image.

2. Ready to use [KAS configurations](https://github.com/elk-audio/kas-configs) for the supported boards.


