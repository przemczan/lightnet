---
icon: material/rocket-launch-outline
---

# Get Started

Lightnet is a DIY project — there is no pre-built kit to buy. Every controller and every panel is something you assemble yourself. This guide walks you end-to-end through the path most people take:

<div class="grid cards" markdown>

-   :material-tools: **1. Build the hardware**

    ---

    What boards you need, how the panels and controller fit together, and where to find schematics and a parts list.

    [:material-arrow-right: Start here](hardware.md)

-   :material-toolbox-outline: **2. Prepare your tools**

    ---

    Install PlatformIO, clone the firmware, plug in a programmer — get a working toolchain before you touch a soldering iron.

    [:material-arrow-right: Toolchain setup](toolchain.md)

-   :material-flash: **3. Flash the firmware**

    ---

    Burn the panel bootloader and fuses, flash each panel, then flash the controller.

    [:material-arrow-right: Flashing](flashing.md)

-   :material-wifi: **4. First boot & Wi-Fi**

    ---

    Power the controller, join its captive portal, point it at your home Wi-Fi, and watch it discover panels.

    [:material-arrow-right: First boot](first-boot.md)

-   :material-cellphone: **5. Use the app**

    ---

    Install the mobile app, let it find the controller via mDNS, and start controlling panels.

    [:material-arrow-right: Use the app](using-the-app.md)

-   :material-compass-outline: **Where to go next**

    ---

    Once it's running, dig into the firmware internals, the HTTP/WebSocket API, or the mobile codebase.

    [:material-arrow-right: Next steps](next-steps.md)

</div>

!!! tip "Reading order"
    Follow the steps in order on your first build. After that, the [Firmware](../lightnet-firmware/index.md) and [App](../lightnet-mobile/index.md) sections are the reference docs you'll keep coming back to.

!!! info "Three audiences, one guide"
    - **Builder** — you are assembling the hardware and flashing it. Stay on this page through step 5.
    - **User** — someone built the hardware for you; you just need the app. Skip to [step 5](using-the-app.md).
    - **Developer** — you want to read the code or extend it. Skim steps 1–4, then jump to [Where to go next](next-steps.md).
