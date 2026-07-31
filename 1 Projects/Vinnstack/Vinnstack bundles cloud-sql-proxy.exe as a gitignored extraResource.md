---
title: "Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource"
created: 2026-07-07
type: lesson
status: archived
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [vinnstack, electron-builder, cloud-sql-proxy, gitignore, gotcha]
---

# Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource

> [!warning] Superseded same day
> This premise no longer holds. A same-day commit (`fbace24`) removed the bundling requirement entirely — see [[Dead bundling config outlives the runtime code that read it]] for what replaced it and the cleanup that followed. Kept for history.

In the vinnstack repo, `cloud-sql-proxy.exe` was deliberately listed in `.gitignore` as a "local dev only" binary — never committed. But `package.json`'s electron-builder `build.extraResources` config bundled it into the packaged app from the repo root at package time.

This meant a fresh checkout (e.g. in CI) had no `cloud-sql-proxy.exe`, and `electron-builder` packaging would fail unless the pipeline first downloaded it, e.g. from `https://storage.googleapis.com/cloud-sql-connectors/cloud-sql-proxy/<version>/cloud-sql-proxy.x64.exe`. The local scripts (`scripts/build-exe.ps1`, `scripts/connect-cloud-db.ps1`) both hard-failed with a helpful message if the file was missing rather than auto-fetching it — CI had to do the fetch itself.

## Related
[[Cross-building Electron Windows exe on Linux needs wine]]
[[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
[[Dead bundling config outlives the runtime code that read it]]
