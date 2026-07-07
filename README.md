# Lightnet Documentation Hub

[![Deploy](https://github.com/przemczan/lightnet/actions/workflows/deploy.yml/badge.svg)](https://github.com/przemczan/lightnet/actions)

MkDocs hub site for the Lightnet project — a modular addressable lighting system. This repo hosts the landing page and shared reference docs. Firmware and mobile docs live in their own repos and are pulled in at build time via `mkdocs-monorepo-plugin`.

## Repositories

| Repo | Description |
|---|---|
| [lightnet](https://github.com/przemczan/lightnet) | This hub — landing page, shared glossary, FAQ, release notes |
| [lightnet-firmware](https://github.com/przemczan/lightnet-firmware) | ESP32 controller + ATmega panel firmware |
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
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Open [http://localhost:8000](http://localhost:8000).

### Previewing the versioned site

`mkdocs serve` is the normal editing loop — fast rebuilds, live reload — but it
renders only your working tree and doesn't show the version switcher or
aliases. To preview the site as it appears on GitHub Pages:

```bash
git fetch origin gh-pages:gh-pages
mike serve
```

This serves the already-committed `gh-pages` branch, not uncommitted edits.
To try out a new version locally before publishing, run
`mike deploy <version> <alias>` without `--push` first, then `mike serve`.
Never run `mike deploy ... --push` locally against `origin` unless you mean
to publish for real.

Preview/beta versions published via the `Deploy Preview Docs` GitHub Action
are not auto-expired — remove them once obsolete with
`mike delete <version> --push`.

## License

Firmware and mobile app are licensed under [GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html).
