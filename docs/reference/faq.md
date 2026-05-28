---
icon: material/help-circle-outline
---

# Reference: FAQ

## General Questions

### What is Lightnet?

Lightnet is a modular addressable lighting system with a controller and multiple panels. The controller manages animations, exposes APIs, and communicates with panels. Panels are simple devices that receive commands and display animations.

See [Firmware Overview](../lightnet-firmware/overview.md) and [Mobile App Overview](../lightnet-mobile/overview.md) for more.

### What license does Lightnet use?

Both the firmware and mobile app are released under the **GNU General Public License v3.0 (GPL-3.0)**. You are free to use, modify, and distribute the source code, provided that derivative works are also distributed under GPL-3.0.

- Firmware source: [przemczan/lightnet-firmware](https://github.com/przemczan/lightnet-firmware)
- Mobile app source: [przemczan/lightnet-mobile](https://github.com/przemczan/lightnet-mobile)

### Can I use Lightnet without the mobile app?

Yes. The Lightnet firmware exposes HTTP and WebSocket APIs that any client can consume — web browsers, custom scripts, other mobile apps, etc. The provided KMP app is just one example client.

### How many panels can Lightnet support?

The firmware supports up to 254 panels (I²C addresses 0–253) per controller. In practice, the limit is determined by physical space and power supply, I²C bus speed and frame timing, and controller SRAM (panel state storage).

See [Firmware Architecture](../lightnet-firmware/architecture.md) for details.

### Can panels run animations without the controller?

No. Panels are "headless" devices that depend entirely on the controller for discovery and topology setup, animation definitions, timing and synchronisation, and external API access. Panels execute animations locally, but they don't initiate them.

---

## Firmware Development

### How do I build the firmware?

Use PlatformIO:

```bash
pio run -e initializer_esp32              # Build controller
pio run -e panel_atmega328p               # Build panel
pio run --target upload                   # Build and flash
```

See [Getting Started](../lightnet-firmware/getting-started.md) for full instructions.

### What are the differences between ESP8266 and ESP32 controllers?

=== "ESP8266"
    - Older, smaller, cheaper
    - Good for simple setups
    - Single-core, limited RAM

=== "ESP32"
    - Newer, dual-core, more powerful
    - Better for large animations and heavy I²C load
    - Slightly different pin assignments

Pin assignments differ — see [Hardware](../lightnet-firmware/hardware.md).

### How do I update the firmware for panels?

=== "HTTP OTA"
    ```bash
    curl -X POST http://lightnet-XXXX.local/api/firmware/panels \
      --data-binary @panel_fw.bin
    ```

=== "Serial OTA"
    ```bash
    python tools/flash_panels_serial.py <port> <firmware.bin>
    ```

=== "Direct programming"
    Use USBasp directly on panel bootloader environments via PlatformIO.

See [OTA & Updates](../lightnet-firmware/ota.md) for details.

### How do I debug firmware issues?

Enable serial debug output at 57600 baud:

```bash
pio device monitor -e initializer_esp32
```

`DEBUG=1` is already set globally in `common_env_data.build_flags`. Use WebSocket queries to inspect panel state. See [Troubleshooting](../lightnet-firmware/troubleshooting.md).

---

## Mobile App Development

### How do I get started with the mobile app?

=== "Android"
    1. Connect an Android device (API 24+) or start an emulator
    2. Run `.\gradlew.bat :composeApp:installDebug`
    3. Launch the **Lightnet** app on the device

=== "iOS"
    1. Open `iosApp/iosApp.xcodeproj` in Xcode
    2. Run the `iosApp` scheme on a simulator or device

See [Getting Started](../lightnet-mobile/getting-started.md) for full instructions.

### Does the app work on Android and iOS?

Yes. The app is built with Kotlin Multiplatform (Compose Multiplatform) and targets both platforms from a single shared codebase.

### How does the app find Lightnet devices?

The app uses mDNS (multicast DNS) to discover Lightnet controllers on the local network. Devices are advertised as `lightnet-<chipid>.local` with service `_lightnet._tcp`.

See [Connectivity](../lightnet-mobile/connectivity.md) for details.

### Can the app control multiple Lightnet devices?

Yes. The app can connect to different controllers sequentially. Multi-device simultaneous control is a potential future feature.

---

## API Questions

### What is the difference between the HTTP and WebSocket APIs?

| | HTTP | WebSocket |
|---|---|---|
| Format | JSON REST | Binary |
| Use for | Configuration, discovery, state queries | Real-time panel control, reactive triggers |
| Latency | ~5 ms | Sub-millisecond |

Use HTTP for setup and WebSocket for interactive control. See [API Reference](../lightnet-firmware/api.md).

### Can I trigger animations from an external service?

Yes. Use the HTTP API to start scenes or the WebSocket API to send beat triggers. You can integrate Lightnet with music services, smart home systems, etc.

```bash
# Play a stored scene by name
curl -X POST http://lightnet-XXXX.local/api/scenes/my-scene/play

# Send a beat trigger via HTTP (use WebSocket ANIMATION_TRIGGER for lower latency)
curl -X POST http://lightnet-XXXX.local/api/animations/trigger \
  -H "Content-Type: application/json" \
  -d '{"group": 1, "value": 200}'
```

### How do I create custom scenes and palettes?

```bash
# Create a palette
curl -X POST http://lightnet-XXXX.local/api/palettes \
  -H "Content-Type: application/json" \
  -d '{"name":"custom","stops":[[0,"#000000"],[255,"#FF0000"]]}'

# Save a scene
curl -X POST http://lightnet-XXXX.local/api/scenes \
  -H "Content-Type: application/json" \
  -d '{...scene JSON...}'
```

See [Animations & Scenes](../lightnet-firmware/animations.md) for the full JSON schema.

---

## Network & Connectivity

### Do the controller and mobile app need to be on the same WiFi network?

Yes. The app discovers devices via mDNS, which requires local network access. Both must be on the same WiFi network (or subnet if mDNS is bridged).

### Can I access Lightnet over the internet?

No. Lightnet is designed for local network use only.

### What if mDNS discovery doesn't work?

!!! tip "Fallback steps"
    1. Verify both devices are on the same WiFi network
    2. Check that mDNS service is running on the controller (it is by default)
    3. Try accessing the controller by IP directly: `http://<controller-ip>/api/appearance`
    4. On iOS, check that local network access is allowed in Privacy settings

---

## Getting Help

Not answered here? Check:

- [Troubleshooting](../lightnet-firmware/troubleshooting.md) for firmware issues
- [Connectivity](../lightnet-mobile/connectivity.md) for connection problems
- [Architecture](../lightnet-firmware/architecture.md) for design deep-dives
- [API Reference](../lightnet-firmware/api.md) for endpoint details

See the [Glossary](glossary.md) for key terminology.
