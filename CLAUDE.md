# CLAUDE.md — lightnet

## What this is

MkDocs hub site for the Lightnet project. This repo contains only the hub `mkdocs.yml`, the landing page (`docs/index.md`), and the shared reference section (`docs/reference/`). Firmware and mobile docs live in their own repos and are pulled in via `mkdocs-monorepo-plugin`.

## Structure

```
lightnet/
  mkdocs.yml           ← hub config with !include for child repos
  requirements.txt     ← pip deps (mkdocs-material, mkdocs-monorepo-plugin)
  docs/
    index.md           ← landing page
    reference/
      glossary.md
      faq.md
      release-notes.md
```

Child repos (must be cloned side-by-side):
- `../lightnet-firmware/` — firmware docs (8 pages under `docs/`)
- `../lightnet-mobile/` — mobile app docs (4 pages under `docs/`)

## Building locally

```bash
pip install -r requirements.txt
mkdocs serve
```

Requires `../lightnet-firmware/` and `../lightnet-mobile/` to exist at their expected relative paths.

## What lives where

| Content | Location |
|---|---|
| Firmware docs | `../lightnet-firmware/docs/` |
| Mobile docs | `../lightnet-mobile/docs/` |
| Shared glossary, FAQ, release notes | `docs/reference/` (this repo) |
| Hub homepage | `docs/index.md` (this repo) |

**Do not add firmware or mobile content to this repo.** Edit the child repo directly — the hub picks up changes automatically at build time.

## Reference section notes

- `glossary.md` — cross-cutting terms spanning both stacks; keep here
- `faq.md` — update the mobile section if the KMP app's stack changes
- `release-notes.md` — currently a placeholder; when versioned releases ship, consider splitting into per-repo changelogs pulled via `!include`
