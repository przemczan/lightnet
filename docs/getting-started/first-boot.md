---
icon: material/wifi
---

# 4. First boot & Wi-Fi

With a freshly-flashed controller and at least one panel connected, you're ready to power the system up and join it to your home network.

## Power the controller

Connect 5 V to the controller and panels. On first boot the controller:

1. Mounts its on-chip flash filesystem (SPIFFS)
2. **Pings each edge** to discover connected panels
3. Assigns each discovered panel an index and a built-in self-test fade
4. Starts a **Wi-Fi captive portal** because no credentials are saved yet

The status LED on the controller blinks while discovery and setup are running.

!!! info "All your panels should briefly flash white"
    During the self-test step the controller fades every discovered panel up and back down. If a panel stays dark, check its wiring or its edge connection.

## Join the captive portal

From your phone or laptop, join the Wi-Fi network broadcast by the controller:

| | |
|---|---|
| **SSID** | `Lightnet-Controller` |
| **Password** | _(open, no password)_ |
| **Portal page** | opens automatically; otherwise visit `http://192.168.4.1` |

In the portal:

1. Pick your home Wi-Fi network
2. Enter the password
3. Save

The controller reboots and joins your network. Once connected, it is discoverable on the network as:

```
http://lightnet-XXXX.local
ws://lightnet-XXXX.local/ws
```

where `XXXX` is the chip ID (printed in the serial log and in the captive portal). This name is published over mDNS as service `_lightnet._tcp`, so the mobile app finds it automatically.

## Verify with HTTP

A quick curl confirms the controller is reachable:

```bash
curl http://lightnet-XXXX.local/api/appearance
```

You should get back a JSON object like:

```json
{"brightness":192,"baseColors":["#FFFFFF","#000000","#000000"],"palette":"userColors"}
```

If that works, the controller is fully online. The full API surface lives in [Firmware → API Reference](../lightnet-firmware/api.md).

??? failure "If the captive portal never appears"
    - Make sure the controller is actually powered (status LED visible)
    - Wait ~20 s after first power-on — the portal takes a moment to start
    - Forget the `Lightnet-Controller` network on your device and reconnect

??? failure "If the controller is unreachable at `lightnet-XXXX.local`"
    Use the IP address printed in the serial monitor instead. mDNS sometimes requires extra setup on Windows or on restrictive networks — see the troubleshooting notes in [Firmware → Troubleshooting](../lightnet-firmware/troubleshooting.md).

---

[:material-arrow-right: Next: Use the app](using-the-app.md){ .md-button .md-button--primary }
