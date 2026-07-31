---
ai_hash: c2139a9adc89063f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- 'Vinnstack exe won''t open without a pre-set databaseUrl (chicken-and-egg: it says
  open Settings but quits first)'
- Vinnstack exe won't open without a pre-set databaseUrl (chicken-and-egg it says
  open Settings but quits first)
created: 2026-07-15
entities:
- electron/main.js
- startNextServer()
- config.json.databaseUrl
- process.env.DATABASE_URL
- lib/core/db.ts
- pool()
- DATABASE_URL
- Interrogation Room
- Chat
- Skills
- Notebook
- Graphify
- Ultracode
- Polaris
- cloud-sql-proxy
- gcloud ADC
- claude CLI
- AI chat
- Cloud SQL
- Vinnstack
- Windows 10/11
- Settings
- Electron
- Node
- HTTP 200
- env -u DATABASE_URL
- gcloud
- PATH
- UI
- Bug
- Fix
- Testing gotcha
- DB-backed features
- startup gate
- window
- Principle
- optional DB
- database connection
- remedy
- Electron exe
- Projects/Vinnstack/Testing the packaged Vinnstack exe needs databaseUrl in config.json,
  pins port 3001, portable stub doesn't inherit ad-hoc env
- ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken'
  — check/clear it before testing
- clean-machine sim
- startup log
- first query
- ambient DATABASE_URL
- missing optional backend
- error message
- fail()
- app.quit()
- DB
source: session 2026-07-15
status: seedling
tags:
- vinnstack
- electron
- startup
- resilience
- clean-install
- database
- ux-bug
title: 'Open-and-degrade beats hard-quit: let a desktop app start without its optional
  DB (Vinnstack clean-Win fix)'
type: lesson
---

# Open-and-degrade beats hard-quit: let a desktop app start without its optional DB (Vinnstack clean-Win fix)

**Bug:** `electron/main.js` `startNextServer()` THREW "No database connection string configured yet. Open Settings and set the database connection (Advanced), then relaunch." (main.js:183) when neither `config.json.databaseUrl` nor `process.env.DATABASE_URL` was set. The throw hit the `whenReady` catch → `fail()` dialog → `app.quit()`. Chicken-and-egg: it told the user to open Settings while quitting, so on a clean Windows 10/11 box (no config, no ambient env) the window never opened and Settings was unreachable.

**Fix (2026-07-15):** `lib/core/db.ts` is LAZY (its `pool()` only connects on the first query), so the startup gate was unnecessary — just skip setting `DATABASE_URL` when absent and open the app. DB-backed features (Interrogation Room) surface a clear error when used; Chat, Skills, Notebook, Graphify, Ultracode and Polaris all work without a DB. Verified on a clean-machine sim (`env -u DATABASE_URL`, no config, no proxy): HTTP 200 + full `startNextServer` happy path in the startup log.

**Testing gotcha that hid this:** an ambient `DATABASE_URL` in the shell made the gate pass on every launch — always test first-run with `env -u DATABASE_URL`.

Other clean-Win prerequisites are runtime/feature-level, NOT startup blockers, and degrade gracefully: cloud-sql-proxy is auto-started best-effort (needs gcloud ADC + the binary on PATH; skipped if absent); the `claude` CLI is needed for AI chat/ultracode; gcloud + Cloud SQL for DB features. The window opens regardless.

**Principle:** a desktop app should OPEN and degrade, never hard-quit before the UI, on a missing optional backend — especially when the remedy (Settings) lives inside that same UI.

## Related

- [[1 Projects/Vinnstack/Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing]]

%% ai-graph-start %%

**Related notes:**
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]
- [[Dead bundling config outlives the runtime code that read it]]
- [[Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]
- [[Do not hardcode a real DB password as a source-code fallback for a packaged desktop app]]

**Relations:**
- Bug — *involved* — electron/main.js
- Bug — *involved* — startNextServer()
- startNextServer() — *threw* — error message
- error message — *mentioned* — config.json.databaseUrl
- error message — *mentioned* — process.env.DATABASE_URL
- error message — *led to* — fail()
- fail() — *led to* — app.quit()
- Bug — *occurred on* — Windows 10/11
- Bug — *is a* — chicken-and-egg problem
- Bug — *prevented* — window
- Bug — *made* — Settings
- Settings — *unreachable* — Bug
- lib/core/db.ts — *is* — LAZY
- lib/core/db.ts — *contains* — pool()
- pool() — *connects on* — first query
- Fix — *is in* — lib/core/db.ts
- Fix — *skips setting* — DATABASE_URL
- Fix — *allows* — Vinnstack
- Vinnstack — *to open* — Fix
- Fix — *removed* — startup gate
- DATABASE_URL — *is set by* — config.json.databaseUrl
- DATABASE_URL — *is set by* — process.env.DATABASE_URL
- DB-backed features — *require* — DB
- DB-backed features — *include* — Interrogation Room
- DB-backed features — *surface clear error when used* — true
- Chat — *works without* — DB
- Skills — *works without* — DB
- Notebook — *works without* — DB
- Graphify — *works without* — DB
- Ultracode — *works without* — DB
- Polaris — *works without* — DB
- Fix — *verified on* — clean-machine sim
- clean-machine sim — *uses* — env -u DATABASE_URL
- startNextServer() — *has* — happy path
- HTTP 200 — *is part of* — happy path
- startup log — *shows* — happy path
- Testing gotcha — *hid* — Bug
- Testing gotcha — *involved* — ambient DATABASE_URL
- Testing gotcha — *can be avoided with* — env -u DATABASE_URL
- cloud-sql-proxy — *is* — auto-started best-effort
- cloud-sql-proxy — *needs* — gcloud ADC
- cloud-sql-proxy — *needs* — binary on PATH
- claude CLI — *is needed for* — AI chat
- claude CLI — *is needed for* — Ultracode
- gcloud — *is needed for* — DB features
- Cloud SQL — *is needed for* — DB features
- Vinnstack — *is a* — desktop app
- Vinnstack — *has* — optional DB
- Vinnstack — *has* — Settings
- Settings — *sets* — database connection
- database connection — *is* — Advanced
- desktop app — *should* — OPEN
- desktop app — *should* — degrade
- desktop app — *should not* — hard-quit
- desktop app — *has* — UI
- Settings — *lives inside* — UI
- Settings — *is a* — remedy
- Principle — *states* — desktop app should OPEN and degrade
- Principle — *states* — desktop app should not hard-quit
- Principle — *applies to* — missing optional backend
- Vinnstack — *is related to* — Projects/Vinnstack/Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env
- Vinnstack — *is related to* — ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing
- Electron exe — *runs as* — Node
- Electron exe — *looks broken* — true
- window — *opens regardless* — true
- startup gate — *was* — unnecessary
- startup gate — *was based on* — DATABASE_URL absence
- Vinnstack — *has* — DB features
- Vinnstack — *has* — AI chat
- Vinnstack — *has* — Ultracode
- Vinnstack — *has* — Interrogation Room
- Vinnstack — *has* — Chat
- Vinnstack — *has* — Skills
- Vinnstack — *has* — Notebook
- Vinnstack — *has* — Graphify
- Vinnstack — *has* — Polaris
- Vinnstack — *is* — app
- Vinnstack — *has* — clean-Win fix

%% ai-graph-end %%