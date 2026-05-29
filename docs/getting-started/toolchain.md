---
icon: material/toolbox-outline
---

# 2. Prepare your tools

You'll build two firmware binaries from the same source tree — one for the controller, one for the panels. The toolchain is the same in both cases: **PlatformIO**, a Python-based wrapper around the Arduino and AVR toolchains.

## Install PlatformIO

The simplest setup is **VS Code + PlatformIO IDE extension**, which installs everything it needs on first run.

=== "VS Code (recommended)"
    1. Install [Visual Studio Code](https://code.visualstudio.com/)
    2. Open the Extensions panel and install **PlatformIO IDE**
    3. Reload — PlatformIO downloads its toolchains the first time you open a project

=== "CLI only"
    ```bash
    pip install -U platformio
    pio --version
    ```

You don't need any other Arduino install. PlatformIO manages the compilers, board definitions, and library cache itself.

## Clone the firmware

```bash
git clone https://github.com/przemczan/lightnet-firmware.git
cd lightnet-firmware
```

The repository contains both controller and panel firmware in one tree. The active environment selects which one PlatformIO compiles:

```bash
pio run -e controller_esp32          # build controller for ESP32
pio run -e panel_atmega328pb         # build panel for ATmega328PB
```

A full list of environments lives in [Firmware → Getting Started](../lightnet-firmware/getting-started.md).

## Drivers

=== "Windows"
    - **USBasp** — install the libusb driver via [Zadig](https://zadig.akeo.ie/)
    - **ESP8266 / ESP32 USB-serial** — most boards use CP210x or CH340; PlatformIO usually auto-installs drivers, otherwise grab them from Silicon Labs or WCH

=== "macOS / Linux"
    No driver install is normally required; PlatformIO talks to the programmer directly via `libusb`. On Linux you may need to add yourself to the `dialout` group for serial access.

## Verify the toolchain

Before going further, do a dry-run compile of either target. This downloads the necessary toolchain and confirms everything is wired up.

```bash
pio run -e controller_esp8266
```

If the build finishes with **`SUCCESS`**, you're ready to flash.

??? tip "Optional: serial console"
    During development it's useful to keep a serial monitor open on the controller. The firmware logs at **57600 baud**:

    ```bash
    pio device monitor -e controller_esp8266
    ```

---

[:material-arrow-right: Next: Flash the firmware](flashing.md){ .md-button .md-button--primary }
