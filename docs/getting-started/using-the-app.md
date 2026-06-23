---
icon: material/cellphone
---

# 5. Use the app

The Lightnet mobile app is the friendliest way to interact with a running installation. It speaks the controller's binary WebSocket protocol, draws the discovered panels on screen, and lets you tap, drag, and tint them in real time.

The app is a [Kotlin Multiplatform](https://kotlinlang.org/lp/multiplatform/) project — one codebase, two platforms.

## Get the app

There is no published build yet — you compile it yourself from the [mobile repository](https://github.com/przemczan/lightnet-mobile).

=== "Android"

    Prerequisites: Android SDK with **API 24+** installed, plus a connected device or emulator.

    ```bash
    git clone https://github.com/przemczan/lightnet-mobile.git
    cd lightnet-mobile
    ./gradlew :composeApp:installDebug
    ```

    Launch the **Lightnet** app on your device.

=== "iOS"

    Prerequisites: **Xcode 14+** on macOS, plus a simulator or physical device.

    ```bash
    git clone https://github.com/przemczan/lightnet-mobile.git
    cd lightnet-mobile
    open iosApp/iosApp.xcodeproj
    ```

    Run the `iosApp` scheme. The Kotlin Multiplatform shared module is compiled automatically by the Xcode build phase.

    !!! note "Discovery on iOS"
        mDNS browsing isn't implemented on iOS yet — devices must be added manually by IP address. The Android side uses `NsdManager` for automatic discovery.

Full build instructions, including running the test suite, live in [App → Getting Started](../lightnet-mobile/getting-started.md).

## First connection

1. Open the app. The **My Devices** screen appears, empty on first run.
2. Tap **Add device** → **Discover**. On Android, the controller shows up as `lightnet-XXXX.local` once mDNS finds it.
3. Tap the device.

The app fetches the panel topology from the controller, lays the panels out in 2-D space, and starts streaming live state.

!!! tip "No hardware? Use the demo"
    The discovery screen has a **Demo Device** entry. It connects to a built-in fake controller (`DemoConnector`) that responds with correct, CRC-valid packets. Every UI flow works against it, so you can explore the app without physical hardware.

## What you can do today

| | |
|---|---|
| **Tap a panel** | Toggles it on/off |
| **Drag across panels** | Paint-toggles a row |
| **Pick a color** | Sets the color of selected panels |
| **Play scenes** | Browse, edit, and play scenes stored on the controller |
| **Manage palettes** | View and edit colour gradients |
| **Power on/off** | Global power state via `POST /api/state/power` |
| **Discover devices** | Browse the local network for controllers (Android) |

The controller also exposes the full HTTP and WebSocket API — you can drive it from `curl`, a browser, your own script, or anything else that speaks HTTP or WebSocket. See [Firmware → API Reference](../lightnet-firmware/api.md).

---

[:material-arrow-right: Next: Where to go next](next-steps.md){ .md-button .md-button--primary }
