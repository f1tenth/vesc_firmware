Custom Firmware Used for VESC Controllers in F1TENTH
====================================================

## Firmware Installation

1. Connect your VESC via USB to your host computer.
1. Open the appropriate version of the VESC Tool.
1. Click Autoconnect.
1. Go to the Firmware section on the left.
1. Go to the Bootloader tab at the top.
1. Click the Upload button in the bottom-right.
1. Click "Write Motor Configuration".
1. Click "Write App Configuration".
1. Go to the Custom File tab.
1. Click the folder icon and browse to the appropriate VESC_TOOL folder and firmware binary for your VESC.
1. Click the Upload button in the bottom-right.

Once this is complete, wait at least 10 seconds before re-connecting to configure additional parameters.

## Building Your Own

The `bldc` firmware requires the `gcc-arm-none-eabi`.
In order to avoid causing problems with your environment, it is recommended to do this in Docker.
Once Docker is installed, you can run the following to build your own, custom firmware with the servo out enabled:

```bash
$ cd ~
$ git clone https://github.com/vedderb/bldc.git
$ cd bldc
$ docker run -it --rm -v $(pwd):/root/workspace -w /root/workspace ubuntu:18.04
ubuntu$ apt-get update && apt-get install build-essential gcc-arm-none-eabi binutils-arm-none-eabi
```

After running these commands, your Docker environment will be ready to build the firmware.
However, you will need to modify the `conf_general.h` file to select the firmware you want to build.
Since [March 31, 2021](https://github.com/vedderb/bldc/commit/fcb7e1215875cd44de676ca6323d2707722d85a7)
you don't have to enable servo output during firmware building. It can be enabled from VESC_tool (App Settings > PPM).
(For older versions, set `SERVO_OUT_ENABLE` to 1.)
To choose which board to build the firmware for, un-comment the `.h` and `.c` files for your board.
The boards that are currently supported by this repository are:

1. VESC 6 MkIII -> `hw_60.h` and `hw_60.c`. Also uncomment `#define HW60_IS_MK3` (make sure the other `HW60_IS_` lines are commented out).
1. VESC SIX+ -> `hw_60.h` and `hw_60.c`. Also uncomment `#define HW60_IS_MK4` (make sure the other `HW60_IS_` lines are commented out).
1. VESC 6 MkV -> `hw_60.h` and `hw_60.c`. Also uncomment `#define HW60_IS_MK5` (make sure the other `HW60_IS_` lines are commented out).
1. FLIPSKY MiniFSESC 4.20 -> `hw_410.h` and `hw_410.c`.

Once you have modified the `conf_general.h` file as necessary, run the command `make` in your Docker container.
This will generate a file called `build/BLDC_4_ChibiOS.bin` which is the custom firmware.
It is recommended to rename this file to match your hardware and move it out of the Docker container.
You can then use this file with the instructions above to flash your VESC.
