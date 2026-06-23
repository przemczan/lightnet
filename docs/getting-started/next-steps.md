---
icon: material/compass-outline
---

# Where to go next

You have a running Lightnet installation. From here, what you read depends on what you want to do.

<div class="grid cards" markdown>

-   :material-script-text-outline: **Build your own scenes**

    ---

    The animation framework — palettes, layers, groups, color references, reactive music triggers, and every JSON field.

    [:material-arrow-right: Firmware → Animations & Scenes](../lightnet-firmware/animations/concepts.md)

-   :material-api: **Drive Lightnet from your own code**

    ---

    Full HTTP and WebSocket reference, including the 14-byte binary packet format and CRC algorithm.

    [:material-arrow-right: Firmware → API Reference](../lightnet-firmware/api.md)

-   :material-sitemap: **Understand the internals**

    ---

    Two-binary source layout, library structure, I²C protocol, discovery sequence, controller boot.

    [:material-arrow-right: Firmware → Architecture](../lightnet-firmware/architecture.md)

-   :material-cellphone-cog: **Hack on the mobile app**

    ---

    Package layout, the device domain layer, the Compose visualiser, and the `DemoConnector` test harness.

    [:material-arrow-right: App → Development](../lightnet-mobile/development.md)

-   :material-cloud-download-outline: **Manage updates**

    ---

    Panel OTA via twiboot, serial firmware upload, and controller self-update over ArduinoOTA.

    [:material-arrow-right: Firmware → OTA & Updates](../lightnet-firmware/ota.md)

-   :material-bug-outline: **Something broken?**

    ---

    Debug macros, common symptoms, and how to inspect panel state over WebSocket.

    [:material-arrow-right: Firmware → Troubleshooting](../lightnet-firmware/troubleshooting.md)

</div>

## Quick reference

- [Glossary](../reference/glossary.md) — every term Lightnet uses, in one place
- [FAQ](../reference/faq.md) — common questions about scope, networking, panels-per-controller, and the API
- [Release notes](../reference/release-notes.md) — version history (placeholder until releases ship)

## Contribute

Lightnet is open source under [GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html). Three repositories:

| Repo | What lives there |
|---|---|
| [`lightnet`](https://github.com/przemczan/lightnet) | This documentation hub |
| [`lightnet-firmware`](https://github.com/przemczan/lightnet-firmware) | Controller + panel firmware |
| [`lightnet-mobile`](https://github.com/przemczan/lightnet-mobile) | Kotlin Multiplatform app |

Issues and pull requests are welcome on all three.
