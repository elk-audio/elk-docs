# Supported Hardware architectures

## Open-source version

The open-source version is supported only for the Raspberry Pi 4 Single Board Computer. Up to version 0.10, we used to support the RPi3 as well but that is not maintained anymore. It is still possible to build images for RPi3 with some effort, though.

## Commercial version

The full Elk Audio OS, including the Xenomai low-latency kernel, audio drivers and RASPA userspace library, is available under a commercial license for these embedded SOCs (listed roughly in order of CPU power):

  * [STM32MP1](https://www.st.com/en/microcontrollers-microprocessors/stm32mp1-series.html)
  * [NXP i.MX8M](https://www.nxp.com/products/processors-and-microcontrollers/arm-processors/i-mx-applications-processors/i-mx-8-applications-processors/i-mx-8m-family-armcortex-a53-cortex-m4-audio-voice-video:i.MX8M) (Mini & Nano)
  * [Raspberry Pi Compute Module 4](https://www.raspberrypi.com/products/compute-module-4/?variant=raspberry-pi-cm4001000)
  * Any Intel x86 SOC

The i.MX8M SOCs are well supported and are typically a very good choice in terms of price vs performance & features, plus they come with very long-term support from the manufacturer. They include a Cortex-M4/M7 core integrated in the SOC for which we have developed an extensive custom firmware that includes:

  * Low-latency DMA based audio engine
  * USB device audio class 2.0 driver
  * ADAT support (via a small external FPGA)

Apart from the mentioned architectures, we also have experience with other traditional Linux audio embedded systems, using ALSA and PREEMPT_RT.

We can assist you in setting up a system without a Xenomai kernel, which can run Sushi and the other major Elk components on pretty much any SOC that can run Linux, albeit at a higher latency than when Xenomai is used.

