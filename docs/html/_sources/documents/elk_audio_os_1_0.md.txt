# ELK Audio OS v1.0 notes

With ELK Audio OS v1.0 we switched from Xenomai 3 to Xenomai 4 that is also called EVL.

EVL is a major addition to the ELK Audio OS that provides many benefits in terms of performance, scalability and
maintenance of the ELK Audio OS for all the platforms we are supporting.

With ELK audio OS v1.0 there are also other major updates, including:

* Simplified Yocto build process by use of [KAS tool](https://github.com/siemens/kas).

* Yocto layers updated to kirkstone release.

In order to simplify the whole image build starting from source code we also provide:

1. [ELK Audio OS builder](https://github.com/elk-audio/elk-audio-os-builder) docker image.

2. Ready to use [KAS configurations](https://github.com/elk-audio/kas-configs) for the supported boards.

As part of the transition from Xenomai 3 to EVL we didn't support some legacy driver needed for the
Elk Pi hardware. This means that Elk Pi hardware is currently not supported. Also `sensei` application
is not supported since it depends on some driver features.
