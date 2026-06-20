---
icon: material/flash
---

# 3. Flash the firmware

Two binaries to flash, in this order:

1. **Each panel** gets a bootloader, fuses, and the panel firmware (over a programmer)
2. **The controller** gets the controller firmware (over USB serial the first time, then Wi-Fi after that)

After the very first flash, panels are updated **over I²C from the controller** — you only need a programmer once per panel.

---

## Flash the panels

### a. Burn the bootloader and fuses

Each panel needs the [twiboot](https://github.com/orempel/twiboot) bootloader sitting in the upper flash region, plus the right fuse settings for the 16 MHz crystal. This is a one-time operation done with a USBasp connected to the panel's ISP header.

!!! warning "Don't guess the fuses"
    Setting fuses by hand is the single easiest way to brick an ATmega — wrong values can lock the chip out of further programming. Use the PlatformIO targets below; they're known good.

Pick the bootloader environment that matches your panel MCU:

=== "ATmega328P"
    ```bash
    # Fuses (also clears flash)
    pio run -e atmega328p_bootloader -t fuses

    # Burn the twiboot bootloader
    pio run -e atmega328p_bootloader -t upload
    ```

=== "ATmega328PB"
    ```bash
    pio run -e atmega328pb_bootloader -t fuses
    pio run -e atmega328pb_bootloader -t upload
    ```

Repeat for every panel. The expected fuse values, where the bootloader lives in flash, and the OTA protocol it speaks are documented in [Firmware → Hardware](../lightnet-firmware/hardware.md) and [Firmware → OTA & Updates](../lightnet-firmware/ota.md).

### b. Flash the panel application

Still over USBasp:

```bash
pio run -e panel_atmega328pb -t upload
```

The same binary works on both `328P` and `328PB`. The `-D` upload flag preserves the bootloader region on erase.

!!! tip "After the first flash, future updates are wireless"
    Subsequent panel updates do **not** require a programmer. Drop the new `.bin` into the app or hit `POST /api/firmware/panels` on the controller and it will reflash every panel one at a time. See [Firmware → OTA & Updates](../lightnet-firmware/ota.md).

---

## Flash the controller

Plug the controller (ESP8266 or ESP32) into USB and pick the matching environment:

=== "ESP8266 (ESP-12E)"
    ```bash
    pio run -e controller_esp8266 -t upload
    ```

=== "ESP8266 (Wemos D1 Mini Pro)"
    ```bash
    pio run -e controller_wemos -t upload
    ```

=== "ESP32 DevKit"
    ```bash
    pio run -e controller_esp32 -t upload
    ```

Once Wi-Fi is configured (see the next step), the same command flashes over the network:

```bash
pio run -e controller_esp32 -t upload --upload-port lightnet-XXXX.local
```

where `XXXX` is the chip ID printed in the captive portal.

---

## Quick sanity check

After flashing the controller, open a serial monitor at **57600 baud**. You should see boot logs ending in something like:

```
mDNS started: lightnet-XXXX.local
WebSocket: /ws
HTTP: :80
```

If you see that, the firmware is healthy. The next step is getting it on your network.

---

[:material-arrow-right: Next: First boot & Wi-Fi](first-boot.md){ .md-button .md-button--primary }
