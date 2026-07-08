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

## Versioning

Docs are versioned with [mike](https://github.com/jimporter/mike) (`extra.version.provider: mike` in `mkdocs.yml`). `mkdocs serve` is unversioned; use `mike serve` (after `git fetch origin gh-pages:gh-pages`) to preview the versioned site with the version switcher. Stable deploys happen automatically via `.github/workflows/deploy.yml` on push to `main` (rolling `current`/`latest`) and on `v*` tags (permanent version). Beta/preview builds — pinning the hub and/or `lightnet-firmware`/`lightnet-mobile` to feature branches — are published manually via the `Deploy Preview Docs` workflow (`workflow_dispatch`), under a version label that never touches the `latest` alias. Preview versions aren't auto-expired; clean up with `mike delete <version> --push`.

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
