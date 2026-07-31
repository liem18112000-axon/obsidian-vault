---
title: "Vinnstack release: push to main triggers Cloud Build which publishes to GCS latest/ auto-update channel"
created: 2026-07-17
type: howto
status: seedling
source: "Vinnstack session 2026-07-17"
tags: [vinnstack, release, cloud-build, electron-updater, gcs]
---

# Vinnstack release: push to main triggers Cloud Build which publishes to GCS latest/ auto-update channel

How a Vinnstack desktop release actually ships (as of 2026-07):

- Cloud Build trigger **`vinnstack`** (project `klara-infra`, region `europe-west6`) fires on **push to `^main$`** of the Bitbucket repo `axonivy-prod/vinnstack`. The `cloudbuild.yaml` header comment says `main-public` — that is **stale**; the live trigger config watches `main`.
- Pipeline: `npm ci` (+ force-install `@next/swc-win32-x64-msvc` matching next version, since it cross-builds the Windows exe on Linux) → vitest gate → `next build` → `electron-builder --win nsis --publish always` under a wine image → upload with gcloud storage.
- Publishing target: `gs://vinnstack-exe-release`. An immutable per-commit copy under `{SHORT_SHA}/`, PLUS the rolling **`latest/`** channel (`vinnstack-setup.exe` + `.blockmap` + `latest.yml`) that electron-updaters generic provider consumes. `latest.yml` is uploaded with `--cache-control="no-cache, max-age=0"` to bypass GCSs 1h object cache.

**Consequence:** the pipeline uploads to `latest/` regardless of which branch it runs on, so ANY build of this pipeline (auto-fired on main, or a manual `gcloud builds triggers run vinnstack --branch=<x>`) ships an auto-update to ALL installed clients. To release: bump `version` in package.json, land on `main`, push. Watch: `gcloud builds list --region=europe-west6 --project=klara-infra`.

Related: [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]], [[electron-builder only ships paths listed in build.files whitelist]].

## Related

- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]
- [[electron-builder only ships paths listed in build.files whitelist]]
