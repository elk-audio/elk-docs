# Getting Started with the ELK UpCore Development Kit Hardware

This document contains basic information on how to set up ELK UpCore development boards. See also the relevant datasheet for your ELK Rocket UpCore shield, for information on the hardware layout of the board.

If you have an Elk Pi Development Kit board, refer instead to the dedicated step-by-step guide [Getting Started With ELK Pi Development Kit Hardware](getting_started_with_development_kit_elk_pi_hardware.md).

## 1. Installing an image to eMMC

If your boards came with the system already flashed to eMMC, you can skip this point. The information here are written for the Rocket/UpCore development kit, for other systems there might be few differences depending on how boot & interal storage are handled.

  1. Extract the provided link to installation image using 7-Zip or a compatible application
  2. Flash the image to a USB pendrive of at least 8GB using [Win32DiskImager](https://sourceforge.net/projects/win32diskimager/) or similar applications like dd for Linux / macOS
  3. Unmount the USB pendrive from your computer and plug it into one of the available USB ports on the UpCore. Note: if the pendrive is not recognized using the principale USB 3.0 port on the module, try to use one of the two extra USB 2.0 ports available on the extension cable.
  4. Connect the UpCore to either HDMI monitor + USB keyboard or with a serial terminal following the instructions in the next paragraph.
  5. Power on the board and immediately press the "ESC" key to enter the BIOS menu
  6. Navigate to the Boot submenu and choose the USB pendrive as "Boot Option n.1"
  7. Save the configuration and exit the BIOS menu
  8. Now you should see a grub menu with a single entry "boot0" available; the flashing procedure to eMMC is fully automated and once it is done (typically around 5 minutes), you will see some messages on the serial prompt regarding the GPT partitions
  9. Power off the board, remove the USB pendrive and reboot

## 2. Partition layout

The internal eMMC storage on the board is divided into four partitions:

- `boot`    : Boot partition with secondary bootloader and Kernel image
- `rootfs1` : First copy of root filesystem
- `rootfs2` : Second copy of root filesystem
- `udata`   : User data partition

The reason for two distinct root filesystem partitions is due to the power-off safe update mechanism through .swu files. At boot, only one of the two redundant filesystem is mounted (in `/`). When a software update is run, it will put its contents on the root filesystem copy that is not currently mounted and it will change the bootloader configuration to boot from it if the update is run successfully. 

For this reason, it is recommended that you put any file using for temporary development in the user
data partition, which will remain untouched after running a software update. It is also worth noticing that while on development images all the partitions are mounted as read/write, in production images the root filesystems are usually mounted as read-only to minimize NAND wearing and possible corruptions when powering off the device.

## 3. Power up, and next steps

1. Put the SD card in the corresponding slot on the Raspberry Pi.
2. Connect the power supply Micro-USB cable to the Raspberry Pi - it will boot into Linux!

From here on, the steps for connecting, and getting sound output, are the same for all our boards. These are detailed in [Getting First Sound From Development Kit Hardware](get_first_sound_from_devkit_board.md).

