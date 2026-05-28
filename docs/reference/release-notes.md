---
icon: material/tag-outline
---

# Release Notes

!!! info "Placeholder"
    This documentation reflects the current codebase state. Release notes for new features, bug fixes, and breaking changes will be documented here when releases occur.

## Versioning

Lightnet uses Semantic Versioning:

- **Major** (X.0.0): Breaking changes or significant new features
- **Minor** (0.X.0): New features, backwards compatible
- **Patch** (0.0.X): Bug fixes, no new features

## Firmware Protocol Versions

The I²C protocol between controller and panels is versioned independently. Controller and panel firmware must use compatible protocol versions.

!!! warning "Protocol compatibility"
    When flashing panel OTA updates, ensure the panel firmware protocol version matches the controller. Mismatched versions will cause panels to behave incorrectly after discovery. See [Architecture — I²C Protocol](../lightnet-firmware/architecture.md#4-i2c-protocol-internal) for the version table.

For current implementation details, see:

- [Firmware Overview](../lightnet-firmware/overview.md)
- [Mobile App Overview](../lightnet-mobile/overview.md)
