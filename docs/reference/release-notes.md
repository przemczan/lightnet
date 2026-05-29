---
icon: material/tag-outline
---

# Release notes

!!! info "Placeholder"
    Versioned releases haven't started yet. This page reflects the current state of the codebase. When tagged releases ship, per-repo changelogs will live here (likely pulled in via `!include` from the firmware and mobile repos).

## Versioning

Lightnet will follow [Semantic Versioning](https://semver.org/):

- **Major** — breaking changes (e.g. I²C protocol bump, JSON schema bump)
- **Minor** — additive features, backwards-compatible
- **Patch** — bug fixes only

## Firmware protocol versions

The internal I²C protocol between controller and panels is versioned independently of the firmware version itself. The current version is **v4** (`Common/Protocol.hpp` → `VERSION`).

!!! warning "Flash controller and panels together"
    The controller and every panel must agree on the I²C protocol version. When you cross a protocol bump (e.g. v3 → v4), flash the controller and **all** panels in the same session. Mismatched versions cause unpredictable behaviour after discovery completes.

    The full protocol-version table lives in [Firmware → Architecture](../lightnet-firmware/architecture.md#4-i2c-protocol-internal).

## Mobile app protocol

The mobile app's WebSocket binary protocol is at `protocolVersion = 0x0001`. Bumps here will be called out in this file when they happen.
