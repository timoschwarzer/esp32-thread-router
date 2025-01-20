# ESP32 Thread Network Router

This is a minimal (and more or less proof of concept) firmware to make an ESP32-H2 act as a Full Thread Device (FTD). FTDs can act as routers ("repeaters" in simple WiFi terms), extending the whole Thread network to make it possible for Minimal Thread Devices (MTD) to reach the Border Router. MTDs are usually battery powered and are thus restricted from becoming a router.


## Device Setup

Make sure you have an ES32-H2-DevKitM-1! If not, you may need to configure and compile the firmware for your respective board manually. The board I am running this firmware on has "Waveshare ESP32-H2-DEV-KIT-N4" printed on the back.

1. Download the latest binaries from [releases](https://github.com/timoschwarzer/esp32-thread-router/releases)
2. Flash the firmware to your board:
   ```sh
   esptool.py --chip esp32h2 --port /dev/YOUR_TTY_PORT --baud 460800 --before default_reset --after hard_reset write_flash -z --flash_mode dio --flash_freq 48m --flash_size detect 0x0 bootloader.bin 0x8000 partitions.bin 0x10000 firmware.bin
   ```
3. Connect to the board via USB serial

You should be able to see `esp32-thread-router/-; esp32h2` when running the `version` command in the serial console.


## Joining your Thread network

The firmware will spawn an [OpenThread CLI](https://github.com/openthread/openthread/blob/main/src/cli/README.md) and create a new Thread network by default.

To join your existing network, set the active dataset:

```sh
dataset set active 0e0000...
```

After a short while, the ESP should connect to your Thread network. You can see the current state by running the `state` command, which should yield in `child` or `router`.

Your Thread network will automatically select a device as router based on network conditions, so it might take a while before your device becomes a router. If your network conditions are ideal already, your device might never become a router.

As final step, when everything works as desired, you can save the dataset to flash:

```sh
dataset commit active
```
