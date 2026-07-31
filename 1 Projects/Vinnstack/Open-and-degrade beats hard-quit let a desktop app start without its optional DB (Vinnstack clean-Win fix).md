---
title: "Open-and-degrade beats hard-quit: let a desktop app start without its optional DB (Vinnstack clean-Win fix)"
aliases:
  - "Vinnstack exe won't open without a pre-set databaseUrl (chicken-and-egg: it says open Settings but quits first)"
  - "Vinnstack exe won't open without a pre-set databaseUrl (chicken-and-egg it says open Settings but quits first)"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15"
tags: [vinnstack, electron, startup, resilience, clean-install, database, ux-bug]
---

# Open-and-degrade beats hard-quit: let a desktop app start without its optional DB (Vinnstack clean-Win fix)

**Bug:** `electron/main.js` `startNextServer()` THREW "No database connection string configured yet. Open Settings and set the database connection (Advanced), then relaunch." (main.js:183) when neither `config.json.databaseUrl` nor `process.env.DATABASE_URL` was set. The throw hit the `whenReady` catch → `fail()` dialog → `app.quit()`. Chicken-and-egg: it told the user to open Settings while quitting, so on a clean Windows 10/11 box (no config, no ambient env) the window never opened and Settings was unreachable.

**Fix (2026-07-15):** `lib/core/db.ts` is LAZY (its `pool()` only connects on the first query), so the startup gate was unnecessary — just skip setting `DATABASE_URL` when absent and open the app. DB-backed features (Interrogation Room) surface a clear error when used; Chat, Skills, Notebook, Graphify, Ultracode and Polaris all work without a DB. Verified on a clean-machine sim (`env -u DATABASE_URL`, no config, no proxy): HTTP 200 + full `startNextServer` happy path in the startup log.

**Testing gotcha that hid this:** an ambient `DATABASE_URL` in the shell made the gate pass on every launch — always test first-run with `env -u DATABASE_URL`.

Other clean-Win prerequisites are runtime/feature-level, NOT startup blockers, and degrade gracefully: cloud-sql-proxy is auto-started best-effort (needs gcloud ADC + the binary on PATH; skipped if absent); the `claude` CLI is needed for AI chat/ultracode; gcloud + Cloud SQL for DB features. The window opens regardless.

**Principle:** a desktop app should OPEN and degrade, never hard-quit before the UI, on a missing optional backend — especially when the remedy (Settings) lives inside that same UI.

## Related

- [[Testing the packaged Vinnstack exe: needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing]]
