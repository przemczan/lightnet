---
icon: material/help-circle-outline
---

# FAQ

## About Lightnet

### What is Lightnet?

A modular addressable lighting system: one **controller** drives many **panels** over I²C. The controller handles Wi-Fi, the HTTP and WebSocket APIs, and scene playback. Each panel runs animations locally on its ATmega after a single setup packet.

See [Firmware Overview](../lightnet-firmware/overview.md) and [App Overview](../lightnet-mobile/overview.md) for the design tour.

### Is it a kit I can buy?

No — it's a DIY project. You build the boards, flash the firmware, and connect everything yourself. Schematics, PCB layouts, and a bill of materials will be published in the future. For now, follow the [Get Started](../getting-started/index.md) guide.

### What license?

Both firmware and the mobile app are released under **GNU GPL v3.0**. You can use, modify, and distribute the code, provided derivatives are also GPL-3.0.

- Firmware: [przemczan/lightnet-firmware](https://github.com/przemczan/lightnet-firmware)
- Mobile app: [przemczan/lightnet-mobile](https://github.com/przemczan/lightnet-mobile)

### Do I need the mobile app?

No. The controller exposes HTTP and WebSocket APIs — `curl`, a web page, a Python script, Home Assistant, or anything else that talks HTTP can control Lightnet. The mobile app is a convenient client, not a requirement. See [Firmware → API Reference](../lightnet-firmware/api.md).

### How many panels can a controller drive?

The firmware caps panel count at **32 on ESP8266** and **100 on ESP32** (`LIGHTNET_MAX_PANELS` in `Core/Common/LightnetConfig.hpp`). In practice the real limit is whichever you hit first: physical layout, power supply, I²C bus quality, and controller SRAM for per-panel state. The I²C address space (7-bit) would allow up to 254, but the firmware reserves headroom.

See [Firmware → Architecture](../lightnet-firmware/architecture.md) for the bandwidth budget.

### Can a panel run without the controller?

No. Panels are headless — they need the controller for discovery, addressing, palette setup, and any external API. They execute animations locally, but they don't initiate them.

---

## Firmware

### How do I build the firmware?

```bash
pio run -e controller_esp32          # build controller
pio run -e panel_atmega328pb         # build panel
pio run -e controller_esp32 -t upload
```

Full walkthrough: [Get Started → Flash the firmware](../getting-started/flashing.md). Per-environment details: [Firmware → Getting Started](../lightnet-firmware/getting-started.md).

### ESP8266 vs ESP32?

=== "ESP8266"
    - Cheaper, lower-power, single-core
    - Plenty for typical installs
    - Slightly different pin map

=== "ESP32"
    - Dual-core, more SRAM
    - Better headroom for large installs and heavy I²C load
    - Recommended if you can pick

Pin assignments: [Firmware → Hardware](../lightnet-firmware/hardware.md).

### How do I update panel firmware after the first flash?

Three ways, in order of convenience:

=== "Mobile app / HTTP"
    ```bash
    curl -X POST http://lightnet-XXXX.local/api/firmware/panels \
      --data-binary @panel_fw.bin
    ```
    The controller streams the binary to its flash, then reflashes every panel one at a time over I²C.

=== "Serial"
    ```bash
    python tools/flash_panels_serial.py <port> <firmware.bin>
    ```
    Same flow, delivered over the controller's USB serial port instead of Wi-Fi.

=== "Programmer"
    Plug the USBasp in and reflash the panel directly. Only needed when you're modifying the bootloader itself.

See [Firmware → OTA & Updates](../lightnet-firmware/ota.md) for the full bootloader story.

### How do I see what the controller is doing?

Open a serial monitor at **57600 baud**:

```bash
pio device monitor -e controller_esp32
```

Serial debug output is off by default. To enable verbose logs, set `DEBUG=1` as a build flag for the controller environment in `platformio.ini`. Per-subsystem detail (API, bus, discovery, etc.) is then controlled by the `DEBUG_*` sub-switches in `controller.config.hpp`. For panel state, use the `GET_PANELS_STATES` WebSocket command or `GET /api/panels` (see [Firmware → API Reference](../lightnet-firmware/api.md)).

---

## Mobile app

### How do I build the app?

=== "Android"
    ```bash
    ./gradlew :composeApp:installDebug
    ```
    Requires Android SDK with **API 24+** and a connected device or emulator.

=== "iOS"
    Open `iosApp/iosApp.xcodeproj` in Xcode 14+ and run the `iosApp` scheme.

Full walkthrough: [App → Getting Started](../lightnet-mobile/getting-started.md).

### Does the same codebase run on Android and iOS?

Yes — it's built with [Kotlin Multiplatform](https://kotlinlang.org/lp/multiplatform/) and [Compose Multiplatform](https://www.jetbrains.com/lp/compose-multiplatform/). One source set, two targets.

### How does the app find controllers?

Via **mDNS** — Lightnet controllers advertise as `lightnet-<chipid>.local` on service `_lightnet._tcp`. The Android side uses Android's `NsdManager`. iOS discovery isn't implemented yet — add devices manually by hostname.

See [App → Connectivity](../lightnet-mobile/connectivity.md).

### Can I control multiple controllers at once?

Sequentially today — pick a device from the list, connect, do your thing, disconnect, switch. Simultaneous multi-controller control isn't implemented.

---

## API & integration

### HTTP or WebSocket?

| | HTTP | WebSocket |
|---|---|---|
| Format | JSON REST | Binary, 14-byte header + payload |
| Use for | Configuration, scenes, queries | Real-time panel control, reactive triggers |
| Latency | ~5 ms | sub-millisecond |

In short: HTTP for setup, WebSocket for live control. See [Firmware → API Reference](../lightnet-firmware/api.md).

### Can I trigger animations from an external service?

Yes. Music sync, smart-home automations, weather feeds — anything that can hit HTTP works:

```bash
# Play a stored scene (id is 8–10 lowercase alphanumeric chars)
curl -X POST http://lightnet-XXXX.local/api/scenes/abcd1234/play

# Fire a reactive beat (HTTP path; WebSocket is lower-latency)
curl -X POST http://lightnet-XXXX.local/api/animations/trigger \
  -H "Content-Type: application/json" \
  -d '{"group": 1, "value": 200}'
```

### Where's the scene JSON schema?

[Firmware → Animations & Scenes](../lightnet-firmware/animations/concepts.md) is the full reference — every animation type, parameter, validation rule, and worked example.

---

## Network

### Do the controller and phone need to be on the same network?

Yes. mDNS discovery and the WebSocket connection are local-network. If your network bridges mDNS across subnets, that works too — but there is no cloud relay.

### Can I expose Lightnet to the internet?

It's designed for local use. There's no auth on the HTTP or WebSocket APIs — exposing it to the internet would let anyone on the planet drive your lights. If you need remote access, put it behind your VPN.

### mDNS discovery isn't working

!!! tip "Diagnostic steps"
    1. Confirm both devices are on the same Wi-Fi network
    2. Try the controller by IP: `http://<ip>/api/appearance`
    3. Windows: ensure Bonjour is installed (comes with iTunes, or as a standalone Bonjour Print Services install)
    4. iOS: check that local-network access is allowed in Settings → Privacy
    5. Some consumer routers block multicast — try a different SSID or AP

---

## Still stuck?

- [Firmware → Troubleshooting](../lightnet-firmware/troubleshooting.md)
- [App → Connectivity](../lightnet-mobile/connectivity.md)
- [Glossary](glossary.md)
- File an issue: [firmware](https://github.com/przemczan/lightnet-firmware/issues) · [app](https://github.com/przemczan/lightnet-mobile/issues)
