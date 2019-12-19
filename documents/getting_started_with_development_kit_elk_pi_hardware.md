# Elk Pi Hardware

Get Started with the Elk Pi Hardware.

This document contains basic information on how to set up Elk Pi development boards.

Each board ships with 4 short spacers, 4 long spacers, the optional bottom plate and screws.

## 1. Assembly

1. While we do not provide a heat sink for your RPi, we do recommend that you use one. Attach that first, by peeling off the blue protective film from the bottom of the heat sink and apply it on top of the Raspberry Pi CPU:

   ![Figure 1](illustrations/datasheet_images/image25.jpg)

2. Mount the four short f-m spacers into the f-f spacers on the Raspberry Pi:

   ![Figure 2](illustrations/datasheet_images/image14.jpg)

3. Plug the Hat on top of the Raspberry Pi:

   ![Figure 3](illustrations/datasheet_images/image17.jpg)

4. Add the four screws if you want extra stability:

   ![Figure 4](illustrations/datasheet_images/image26.jpg)
   
5. Finally, if you want you can add the optional bottom plate, using the included long spacers and screws. The final assembly then appears as follows:

   ![Figure 4](illustrations/elk_pi_completed_assembly.jpg)

## 2. Flashing the Elk operating system image to the SD card

To get the system running, you need to flash an Elk operating system image onto an SD card and plug it into the Raspberry Pi. The minimum required SD card size is 8GB.

1. Download the compressed image from the provided link. It will have a filename such as: *elk-sika-image-dev-raspberrypi3-64.wic.bz2*.

2. Connect the empty SD card to a computer. If you do not have a computer with an SD/Micro-SD card reader, you will need a USB adapter.

### Windows:

   1. Extract the `.wic` file from the `.bz2` archive using [7Zip](https://www.7-zip.org/download.html) or another compression utility.

   2. Download and install [balenaEtcher](https://www.balena.io/etcher/), or [Win32DiskImager](https://www.raspberrypi.org/downloads/).
   
   3. Flash the extracted `.wic` file to the SD card.

### Linux:
At your convenience, you can either use [balenaEtcher](https://www.balena.io/etcher/), or use plain `dd` with these instructions:

   1. Find out under what name your SD card is listed on your computer, using e.g. `lsblk -f`. On modern Linux distributions it usually is `/dev/sdb`, `/dev/sdc`, or subsequent letters, or sometimes `/dev/mmcblk0`. We will from now on refer to it as `/dev/sdX`.
   
   2. Unmount the SD card if it was already mounted:

   ```bash
     $ sudo umount /dev/sdX*
   ```

   3. **VERY IMPORTANT: double-check that you are using the correct device with dd! Otherwise you could risk wiping out another disk on your machine!** To uncompress and flash onto the SD card run:
      
   ```bash
      $ bzcat elk-sika-image-dev-raspberrypi3-64.wic.bz2 | sudo dd of=/dev/sdX bs=4M status=progress && sync
   ```


   4. The process can take a few minutes, so this is a good time to stretch your legs.

      If for any reason the process fails, just reduce the `bs=4M` speed to `bs=1M` for example, and it should work great.

### macOS:

   Also here you can flash the image using [balenaEtcher](https://www.balena.io/etcher/), using Unarchiver to extract the `.bz2` file first.

## 3. Power up, and next steps

1. Put the SD card in the corresponding slot on the Raspberry Pi.
2. Connect the power supply Micro-USB cable to the Raspberry Pi - it will boot into Linux.

From here on, the steps for connecting, and getting sound output, are the same for all our boards. These are detailed in [Run Elk on Boards](run_elk_on_boards.md).
