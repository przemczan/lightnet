---
icon: material/tag-outline
---

# Release notes

Dated entries below track major/minor releases across the three Lightnet repos. Splitting into per-repo changelogs (pulled in via `!include`) remains a future consideration if this page grows unwieldy.

## v1.0.0 — 2026-08-05

First major release.

- **Firmware** — replaced the controller↔panel I²C bus with a new UART relay transport: multi-hop discovery, per-hop link-ARQ acknowledgments, and a relay OTA bootloader for flashing panels over the relay chain (panel flasher tooling reworked to match). Internal wire protocol (`Protocol::VERSION`) bumped **v6 → v14**.
- **Breaking**: this is a protocol-incompatible change — flash the controller and **every** panel in the same session (see the warning below).
- **Mobile app** — synced the app's I²C/binary packet protocol handling (packet builder, packet types, scene editor round-trip) to match the firmware's new transport.

## Versioning

Lightnet will follow [Semantic Versioning](https://semver.org/):

- **Major** — breaking changes (e.g. wire protocol bump, JSON schema bump)
- **Minor** — additive features, backwards-compatible
- **Patch** — bug fixes only

## Firmware protocol versions

The internal wire protocol carried over the UART relay trunk between controller and panels is versioned independently of the firmware version itself. The current version is **v14** (`Core/Common/ProtocolMeta.hpp` → `Protocol::VERSION`).

!!! warning "Flash controller and panels together"
    The controller and every panel must agree on the wire protocol version. When you cross a protocol bump, flash the controller and **all** panels in the same session. Mismatched versions cause unpredictable behaviour after discovery completes — with the exception of a small set of version-exempt packet types (reset, enter-bootloader, discovery) that let a mismatched panel still be discovered, reset, and reflashed over the relay.

    The full protocol-version table lives in [Firmware → Architecture](../lightnet-firmware/architecture.md#4-wire-protocol-internal).

## Mobile app protocol

The mobile app's WebSocket binary protocol is at `protocolVersion = 0x0001`. Bumps here will be called out in this file when they happen.
