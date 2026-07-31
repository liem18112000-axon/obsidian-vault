---
ai_hash: 3a6d5c936adf7aa1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities:
- Vinnstack
- cloud-sql-proxy.exe
- .gitignore
- package.json
- electron-builder
- build.extraResources
- packaged app
- CI
- Google Cloud Storage
- scripts/build-exe.ps1
- scripts/connect-cloud-db.ps1
- fbace24
- Dead bundling config outlives the runtime code that read it
- Cross-building Electron Windows exe on Linux needs wine
- Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod
- bundling requirement
- cleanup
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: archived
tags:
- vinnstack
- electron-builder
- cloud-sql-proxy
- gitignore
- gotcha
title: Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Dead bundling config outlives the runtime code that read it]]
- [[Cross-building Electron Windows exe on Linux needs wine]]
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]]

**Relations:**
- Vinnstack — *bundles* — cloud-sql-proxy.exe
- cloud-sql-proxy.exe — *is_a* — gitignored extraResource
- cloud-sql-proxy.exe — *is_a* — local dev only binary
- .gitignore — *lists* — cloud-sql-proxy.exe
- package.json — *contains_config_for* — electron-builder
- electron-builder — *uses_config_key* — build.extraResources
- build.extraResources — *configures_bundling_of* — cloud-sql-proxy.exe
- electron-builder — *generates* — packaged app
- packaged app — *expected_to_contain* — cloud-sql-proxy.exe
- CI — *lacked* — cloud-sql-proxy.exe
- electron-builder — *packaging_fails_without* — cloud-sql-proxy.exe
- CI — *downloaded* — cloud-sql-proxy.exe
- cloud-sql-proxy.exe — *downloaded_from* — Google Cloud Storage
- scripts/build-exe.ps1 — *requires* — cloud-sql-proxy.exe
- scripts/connect-cloud-db.ps1 — *requires* — cloud-sql-proxy.exe
- bundling requirement — *superseded_by* — fbace24
- fbace24 — *removed* — bundling requirement
- Dead bundling config outlives the runtime code that read it — *describes* — cleanup
- Vinnstack — *related_to* — Cross-building Electron Windows exe on Linux needs wine
- Vinnstack — *related_to* — Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod
- Vinnstack — *related_to* — Dead bundling config outlives the runtime code that read it

%% ai-graph-end %%