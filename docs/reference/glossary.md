---
icon: material/alphabetical
---

# Glossary

Cross-cutting vocabulary used across firmware and app. If a term has a deeper home page elsewhere, the entry links to it.

## Hardware

Controller
:   The central device in a Lightnet network — an ESP32. It discovers panels, exposes the HTTP and WebSocket APIs over Wi-Fi, runs the scene player, and drives the UART relay trunk that carries all panel traffic. Defined in [Firmware → Architecture](../lightnet-firmware/architecture.md).

Panel
:   A leaf device — an ATmega328P or 328PB driving one [WS2812](#term-ws2812) LED. Panels have up to 5 edge connectors and run animations locally between updates from the controller. Each panel is a **store-and-forward repeater**, not a node on a shared bus — it only ever talks to its own physical neighbours.

Edge
:   A physical connector on a panel (or controller). Each edge carries power and a point-to-point UART link to that one neighbour — the same link handles discovery and all runtime traffic, with no separate ping phase.

Topology
:   The connectivity graph. Lightnet uses a **tree** rooted at the controller — one panel attaches directly, others fan out. No cycles, no rings.

## Animation system

Scene
:   Top-level playback unit. A scene contains one or more layers, can loop, and is stored as a binary record in `/data/scenes.db` on the controller (`SceneStore`). HTTP bodies use scene JSON. See [Firmware → Animations & Scenes](../lightnet-firmware/animations/concepts.md).

Layer
:   An independent animation track inside a scene. Targets a set of panels, belongs to a [group](#term-group), and runs a sequence of [steps](#term-step). Up to 8 layers per scene.

Step { #term-step }
:   One animation segment in a layer's sequence. Either **panel-local** (BREATHE, PULSE, etc. — runs on the ATmega with no per-frame relay traffic) or a **controller runner** (WAVE, RIPPLE, CHASE — computed on the ESP and pushed each frame). Up to 12 steps per layer.

Group { #term-group }
:   A synchronisation unit. Panels in the same group fire animations simultaneously via a general-call flood (broadcast address) over the relay trunk. Group IDs 1–254 are valid; 0 is reserved.

Palette
:   A 1–16 stop gradient of (position, RGB) entries. The controller linearly interpolates between stops to produce a continuous 256-entry colour ramp. Built-in palettes (`Rainbow`, `Lava`, `Ocean`, …) ship with the firmware; user palettes are saved to `/data/palettes.db`.

ColorRef
:   The single tagged way to specify a colour in an animation. Three forms:

    - **Inline RGB** — `"#FF0000"` or `{r,g,b}`
    - **Palette position** — `{"palette": 128}`, sampled at frame time
    - **Base-colour slot** — `{"useColor": 0}` (primary), 1 (secondary), 2 (tertiary)

Animation runner
:   Controller-computed animation. Calculates per-panel brightness or colour each frame and pushes it over the relay trunk. **WAVE** sweeps a brightness envelope across the panel list, **RIPPLE** expands from an origin panel, **CHASE** moves a single lit panel through the list.

## Networking & protocol

mDNS
:   Local service discovery. Lightnet controllers advertise as `lightnet-<chipid>.local` on service `_lightnet._tcp`, so clients on the same network find them without a fixed IP.

HTTP API
:   JSON REST API on port 80. Used for configuration, scenes, palettes, appearance, and firmware uploads. Full surface: [Firmware → API Reference](../lightnet-firmware/api.md).

WebSocket
:   Binary protocol at `ws://lightnet-<chipid>.local/ws`. Sub-millisecond panel control and reactive triggers. 14-byte header with CRC-16/IBM over header and payload. Full schema: [Firmware → API Reference](../lightnet-firmware/api.md).

Wire protocol
:   The internal packet protocol carried over the UART relay trunk between the controller and every panel. Custom packed-struct format with CRC-16 framing, defined in `Core/Common/ProtocolMeta.hpp` (included via `Common/Protocol.hpp`). Documented in [Firmware → Architecture](../lightnet-firmware/architecture.md#4-wire-protocol-internal).

General Call
:   A broadcast packet (target address `0`) flooded to every panel in the tree — used for animation start, palette and brightness updates, and reactive triggers.

Discovery
:   The boot-time depth-first walk that maps the panel tree. The controller descends edge by edge over the relay; each newly-reached panel registers and receives a sequential index before the controller backtracks to the next sibling edge. After all panels report, the controller transitions to its main loop.

Relay trunk
:   The UART link between the controller and the first panel, and by extension the whole chain of point-to-point panel-to-panel UART hops it feeds. Every panel is a store-and-forward repeater — there is no shared bus.

## Persistence & updates

LittleFS { #term-littlefs }
:   Little Flash File System — the ESP's small on-chip filesystem. Lightnet stores scenes, palettes, appearance settings, and the staged panel firmware blob here.

OTA
:   Wireless firmware update. **Panel OTA** streams pages over the relay trunk to a from-scratch bootloader (`RelayBootloader`) that speaks the same framed wire protocol. **Controller OTA** uses ArduinoOTA over Wi-Fi.

RelayBootloader { #term-relaybootloader }
:   The bootloader resident on each ATmega panel. Accepts firmware pages over the relay's own packet framing (`PACKET_BOOTLOADER_*` types). Entered when the controller sends `PACKET_ENTER_BOOTLOADER` with a magic token and the panel resets into it; deliberately skips protocol-version checks so it can still reflash a version-mismatched panel.

WS2812 { #term-ws2812 }
:   The addressable RGB LED on each panel — single-wire timing-coded protocol, one bit-banged on `PD5`.

---

Don't see something? Check [the FAQ](faq.md) or open an issue on the relevant repo.
