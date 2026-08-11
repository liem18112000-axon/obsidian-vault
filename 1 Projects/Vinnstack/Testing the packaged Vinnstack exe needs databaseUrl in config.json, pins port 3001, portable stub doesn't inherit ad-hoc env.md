---
ai_hash: 7b4dffaf42358980
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities:
- Vinnstack exe
- electron-builder
- portable exe
- SELF-EXTRACTOR
- '%TEMP%'
- Electron app
- detached child
- launching shell/process
- ad-hoc env vars
- VINNSTACK_NEXT_PORT
- DATABASE_URL
- DB connection string
- ~/.agentic-os/config.json
- databaseUrl
- process.env.DATABASE_URL
- repo .env
- electron/main.js
- startNextServer()
- dialog "No database connection string configured yet — open Settings"
- Settings
- port 3001
- next dev
- http://localhost:3001
- Cloud SQL proxy
- gcloud ADC
- Cloud Build
- vitest
- next build
- electron-builder package step
- GCS upload
- runtime wiring
- operator
- user env
source: session 2026-07-14
status: seedling
tags:
- vinnstack
- electron
- portable
- exe
- testing
- gotcha
title: 'Testing the packaged Vinnstack exe: needs databaseUrl in config.json, pins
  port 3001, portable stub doesn''t inherit ad-hoc env'
type: lesson
---

# Testing the packaged Vinnstack exe: needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env

Testing the packaged Vinnstack Electron exe (electron-builder --win portable), 2026-07-14:

- The portable .exe is a SELF-EXTRACTOR: it unpacks to %TEMP% and spawns the real Electron app as a detached child, so the launching shell/process returns exit 0 immediately — that does NOT mean the app crashed. And the extracted child did NOT inherit ad-hoc env vars (VINNSTACK_NEXT_PORT / DATABASE_URL) set in the launching shell, so overrides that way don't take.
- The packaged app needs a DB connection string from ~/.agentic-os/config.json (`databaseUrl`) OR process.env.DATABASE_URL set BEFORE launch. It does NOT read the repo `.env` (that's dev-only). With neither, electron/main.js startNextServer() throws and shows the designed dialog "No database connection string configured yet — open Settings", then quits. So "launches then shows that dialog" = correct unconfigured-first-run behavior, not a bug.
- It pins port 3001 (VINNSTACK_NEXT_PORT overridable only if the env reaches the app). Can't run it alongside a `next dev` also on 3001.

Clean local smoke-test recipe: stop anything on 3001; put a real `databaseUrl` in ~/.agentic-os/config.json (or set DATABASE_URL at a level the portable child inherits — e.g. user env); launch the exe; poll http://localhost:3001 for 200. Cloud SQL proxy is best-effort (needs gcloud ADC) and never blocks the window from opening.

Bigger point: a green Cloud Build (install → vitest → next build → electron-builder package → GCS upload) already proves the exe was built from code that passes tests and compiles; launching it only additionally checks runtime wiring, which needs the machine configured like a real operator's.

%% ai-graph-start %%

**Related notes:**
- [[Open-and-degrade beats hard-quit let a desktop app start without its optional DB (Vinnstack clean-Win fix)]]
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
- [[electron-builder portable self-extracts on launch — use portable.splashImage for that UI-less gap]]
- [[Vinnstack is local-only by design spawned-CLI login + local FS state + single-tenant]]

**Relations:**
- Vinnstack exe — *is packaged by* — electron-builder
- Vinnstack exe — *is a* — portable exe
- portable exe — *is a* — SELF-EXTRACTOR
- portable exe — *unpacks to* — %TEMP%
- portable exe — *spawns* — Electron app
- Electron app — *is a* — detached child
- detached child — *does not inherit* — ad-hoc env vars
- ad-hoc env vars — *include* — VINNSTACK_NEXT_PORT
- ad-hoc env vars — *include* — DATABASE_URL
- Vinnstack exe — *needs* — DB connection string
- DB connection string — *can be from* — ~/.agentic-os/config.json
- ~/.agentic-os/config.json — *contains key* — databaseUrl
- DB connection string — *can be* — process.env.DATABASE_URL
- Vinnstack exe — *does not read* — repo .env
- repo .env — *is for* — dev-only
- electron/main.js — *contains function* — startNextServer()
- startNextServer() — *throws if* — DB connection string is missing
- startNextServer() — *shows* — dialog "No database connection string configured yet — open Settings"
- dialog "No database connection string configured yet — open Settings" — *prompts to open* — Settings
- Vinnstack exe — *pins* — port 3001
- port 3001 — *is default for* — VINNSTACK_NEXT_PORT
- VINNSTACK_NEXT_PORT — *can override* — port 3001
- Vinnstack exe — *cannot run alongside* — next dev
- next dev — *uses* — port 3001
- Vinnstack exe — *listens on* — http://localhost:3001
- DATABASE_URL — *can be set in* — user env
- detached child — *inherits from* — user env
- Cloud SQL proxy — *needs* — gcloud ADC
- Cloud Build — *includes step* — vitest
- Cloud Build — *includes step* — next build
- Cloud Build — *includes step* — electron-builder package step
- Cloud Build — *includes step* — GCS upload
- Cloud Build — *proves* — Vinnstack exe is built from tested and compiled code
- launching Vinnstack exe — *checks* — runtime wiring
- runtime wiring — *needs machine configured like* — operator

%% ai-graph-end %%