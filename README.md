# Lightnet Documentation Hub

[![Deploy](https://github.com/przemczan/lightnet/actions/workflows/deploy.yml/badge.svg)](https://github.com/przemczan/lightnet/actions)

MkDocs hub site for the Lightnet project — a modular addressable lighting system. This repo hosts the landing page and shared reference docs. Firmware and mobile docs live in their own repos and are pulled in at build time via `mkdocs-monorepo-plugin`.

## Repositories

| Repo | Description |
|---|---|
| [lightnet](https://github.com/przemczan/lightnet) | This hub — landing page, shared glossary, FAQ, release notes |
| [lightnet-firmware](https://github.com/przemczan/lightnet-firmware) | ESP8266/ESP32 controller + ATmega panel firmware |
| [lightnet-mobile](https://github.com/przemczan/lightnet-mobile) | Kotlin Multiplatform app for Android and iOS |

## Building locally

Clone all three repos side-by-side:

```
parent/
  lightnet/   ← this repo
  lightnet-firmware/
  lightnet-mobile/
```

Then:

```bash
pip install -r requirements.txt
mkdocs serve
```

Open [http://localhost:8000](http://localhost:8000).

## License

Firmware and mobile app are licensed under [GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html).
