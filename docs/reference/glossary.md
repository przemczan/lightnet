---
icon: material/alphabetical
---

# Reference: Glossary

## Key Terminology

Controller
:   The central device in the Lightnet network, typically an ESP8266 or ESP32. The controller discovers panels via GPIO ping handshakes, manages all panel communication over I²C, exposes HTTP and WebSocket APIs to external clients, runs animations and scenes, and handles over-the-air firmware updates for panels.

Panel
:   A leaf device in the Lightnet network, typically an ATmega microcontroller (328P or 328PB). Each panel has one addressable RGB LED (WS2812), connects to the network via a tree topology (0–3 edges), receives commands and animation data from the controller, and executes animations locally to minimise I²C traffic.

Edge
:   A physical connector on a panel that can connect to another panel or the controller. Each edge carries both power and a single-wire ping line used during discovery. Panels have 0–3 edges; the controller has N edges (one per connected panel).

Topology
:   The physical or logical structure of how panels are connected. Lightnet uses a **tree topology** rooted at the controller — one panel connects directly (edge 0), others cascade from it (edges 1, 2), forming a tree with no cycles.

Group (Animation Group)
:   A synchronisation unit for animations. Panels in the same group fire animations simultaneously (±2.5 µs jitter via I²C General Call). Groups are independent — a panel can run animations in multiple groups at once without interference. Valid group IDs: 1–254 (0 is reserved).

Scene
:   A top-level playback unit containing one or more layers. Scenes are stored as JSON files on the controller's SPIFFS and can loop indefinitely or play once, have multiple layers running concurrently, be saved/loaded/triggered via HTTP API, and play music-synchronised animations with reactive triggers.

Layer
:   An independent animation track inside a scene. Each layer targets a set of panels (all, a list, or an exclude list), belongs to a single group ID, runs a sequence of steps back-to-back, and can override the scene-level palette.

Step
:   A single animation segment within a layer's sequence. Steps advance automatically when `durationMs` elapses. A step is either **panel-local** (runs entirely on the ATmega, e.g. BREATHE, BLINK) or **controller-computed** (computed on the ESP and sent per-frame, e.g. WAVE, RIPPLE).

Palette
:   A 16-stop gradient of RGB colors used for smooth color transitions. Palettes store position (0–255) and RGB for each stop, are linearly interpolated to produce a 256-entry gradient, and can be built-in (rainbow, lava, ocean, etc.) or user-defined. Persisted on SPIFFS or synthesized from base colors.

Animation Runner
:   A controller-computed animation that calculates per-panel brightness or color each frame and sends updates via I²C. Runners include **WAVE** (traveling wave), **RIPPLE** (radial ripple from a center panel), **CHASE** (sequential chase), and **REACTIVE** (beats triggered via WebSocket).

ColorRef
:   A unified way to specify colors in animations, resolved at frame time:

    - **Inline RGB**: `{"r":255, "g":0, "b":0}` or `"#FF0000"`
    - **Palette position**: `{"palette":128}` — samples the active gradient
    - **Base color**: `{"useColor":0}` — primary, secondary, or tertiary slot

OTA (Over-the-Air)
:   Firmware update mechanism. **Panel OTA**: panels enter the twiboot bootloader and receive new firmware via I²C. **Controller OTA**: the controller receives new firmware via ArduinoOTA (WiFi) or serial upload.

twiboot
:   A custom bootloader for ATmega panels that allows wireless (I²C) firmware updates. Panels enter twiboot when they receive an `ENTER_BOOTLOADER` command, write a magic value to EEPROM, and reset.

mDNS (Multicast DNS)
:   Network discovery protocol. Lightnet controllers advertise themselves as `lightnet-<chipid>.local` with service `_lightnet._tcp`, allowing mobile apps and clients to find them without manual IP configuration.

WebSocket
:   A bidirectional communication protocol used for real-time, low-latency control. Lightnet exposes a binary WebSocket API at `ws://lightnet-<chipid>.local/ws` for panel commands and reactive triggers.

HTTP API
:   A JSON REST API exposed on port 80 used for device discovery and configuration, appearance control (brightness, palette, colors), scene management (CRUD operations), and firmware update status queries.

I²C Protocol
:   Inter-Integrated Circuit serial bus used for all internal communication between the controller and panels. Lightnet defines a custom protocol with packet structures, CRC validation, and command types.

General Call
:   I²C broadcast to all panels simultaneously (address `0x00`, ±2.5 µs jitter). Used for starting animations in sync across multiple panels, reactive trigger updates, and global palette and brightness changes.

Discovery
:   The process by which the controller identifies all connected panels after boot. The controller sends GPIO ping pulses on each edge; panels respond via PCINT interrupts, register over I²C, and receive sequential addresses. All panels then transition to READY state.

SPIFFS
:   Serial Peripheral Interface Flash File System. The ESP8266/ESP32 uses SPIFFS to store scene JSON files (`/scenes/<name>.json`), palette definitions, appearance configuration, and firmware binaries for panel OTA.

---

See the [FAQ](faq.md) for common developer questions.
