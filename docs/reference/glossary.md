---
icon: material/alphabetical
---

# Glossary

Cross-cutting vocabulary used across firmware and app. If a term has a deeper home page elsewhere, the entry links to it.

## Hardware

Controller
:   The central device in a Lightnet network — an ESP8266 or ESP32. It discovers panels, exposes the HTTP and WebSocket APIs over Wi-Fi, runs the scene player, and brokers all I²C traffic. Defined in [Firmware → Architecture](../lightnet-firmware/architecture.md).

Panel
:   A leaf device — an ATmega328P or 328PB driving one [WS2812](#term-ws2812) LED. Panels have 0–3 edge connectors and run animations locally between updates from the controller.

Edge
:   A physical connector on a panel (or controller). Each edge carries power and a single-wire ping line used during discovery. After discovery, I²C handles all communication; the ping line goes idle until reset.

Topology
:   The connectivity graph. Lightnet uses a **tree** rooted at the controller — one panel attaches directly, others fan out. No cycles, no rings.

## Animation system

Scene
:   Top-level playback unit. A scene contains one or more layers, can loop, and is stored as JSON on the controller's [SPIFFS](#term-spiffs) under `/scenes/<name>.json`. See [Firmware → Animations & Scenes](../lightnet-firmware/animations/concepts.md).

Layer
:   An independent animation track inside a scene. Targets a set of panels, belongs to a [group](#term-group), and runs a sequence of [steps](#term-step). Up to 8 layers per scene.

Step { #term-step }
:   One animation segment in a layer's sequence. Either **panel-local** (BREATHE, PULSE, etc. — runs on the ATmega with no per-frame I²C) or a **controller runner** (WAVE, RIPPLE, CHASE — computed on the ESP and pushed each frame). Up to 12 steps per layer.

Group { #term-group }
:   A synchronisation unit. Panels in the same group fire animations simultaneously (±2.5 µs jitter via I²C General Call). Group IDs 1–254 are valid; 0 is reserved.

Palette
:   A 1–16 stop gradient of (position, RGB) entries. The controller linearly interpolates between stops to produce a continuous 256-entry colour ramp. Built-in palettes (`rainbow`, `lava`, `ocean`, …) ship with the firmware; user palettes are saved to [SPIFFS](#term-spiffs).

ColorRef
:   The single tagged way to specify a colour in an animation. Three forms:

    - **Inline RGB** — `"#FF0000"` or `{r,g,b}`
    - **Palette position** — `{"palette": 128}`, sampled at frame time
    - **Base-colour slot** — `{"useColor": 0}` (primary), 1 (secondary), 2 (tertiary)

Animation runner
:   Controller-computed animation. Calculates per-panel brightness or colour each frame and pushes it over I²C. **WAVE** sweeps a brightness envelope across the panel list, **RIPPLE** expands from an origin panel, **CHASE** moves a single lit panel through the list.

## Networking & protocol

mDNS
:   Local service discovery. Lightnet controllers advertise as `lightnet-<chipid>.local` on service `_lightnet._tcp`, so clients on the same network find them without a fixed IP.

HTTP API
:   JSON REST API on port 80. Used for configuration, scenes, palettes, appearance, and firmware uploads. Full surface: [Firmware → API Reference](../lightnet-firmware/api.md).

WebSocket
:   Binary protocol at `ws://lightnet-<chipid>.local/ws`. Sub-millisecond panel control and reactive triggers. 14-byte header with CRC-16/IBM over header and payload. Full schema: [Firmware → API Reference](../lightnet-firmware/api.md).

I²C protocol
:   The internal bus between controller and panels. Custom packet format defined in `Common/Protocol.hpp` with packed structs and CRC validation. Documented in [Firmware → Architecture](../lightnet-firmware/architecture.md).

General Call
:   I²C broadcast (address `0x00`) — every panel receives it simultaneously. Lightnet uses it for animation start, palette and brightness updates, and reactive triggers.

Discovery
:   The boot-time process that walks the panel tree. The controller pings each edge in turn; receiving panels register over I²C and receive a sequential index. After all panels report, the controller transitions to its main loop.

## Persistence & updates

SPIFFS { #term-spiffs }
:   SPI Flash File System — the ESP's small on-chip filesystem. Lightnet stores scenes, palettes, appearance settings, and the staged panel firmware blob here.

OTA
:   Wireless firmware update. **Panel OTA** uses the [twiboot](#term-twiboot) bootloader and pushes pages over I²C. **Controller OTA** uses ArduinoOTA over Wi-Fi.

twiboot { #term-twiboot }
:   A bootloader for ATmega panels that accepts firmware pages over I²C. Lives in the upper 4 KB of flash. Entered when the controller sends an `ENTER_BOOTLOADER` command and the panel writes a magic value to EEPROM before resetting.

WS2812 { #term-ws2812 }
:   The addressable RGB LED on each panel — single-wire timing-coded protocol, one bit-banged on `PD5`.

---

Don't see something? Check [the FAQ](faq.md) or open an issue on the relevant repo.
