---
title: "Testing the packaged Vinnstack exe: needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [vinnstack, electron, portable, exe, testing, gotcha]
---

# Testing the packaged Vinnstack exe: needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env

Testing the packaged Vinnstack Electron exe (electron-builder --win portable), 2026-07-14:

- The portable .exe is a SELF-EXTRACTOR: it unpacks to %TEMP% and spawns the real Electron app as a detached child, so the launching shell/process returns exit 0 immediately — that does NOT mean the app crashed. And the extracted child did NOT inherit ad-hoc env vars (VINNSTACK_NEXT_PORT / DATABASE_URL) set in the launching shell, so overrides that way don't take.
- The packaged app needs a DB connection string from ~/.agentic-os/config.json (`databaseUrl`) OR process.env.DATABASE_URL set BEFORE launch. It does NOT read the repo `.env` (that's dev-only). With neither, electron/main.js startNextServer() throws and shows the designed dialog "No database connection string configured yet — open Settings", then quits. So "launches then shows that dialog" = correct unconfigured-first-run behavior, not a bug.
- It pins port 3001 (VINNSTACK_NEXT_PORT overridable only if the env reaches the app). Can't run it alongside a `next dev` also on 3001.

Clean local smoke-test recipe: stop anything on 3001; put a real `databaseUrl` in ~/.agentic-os/config.json (or set DATABASE_URL at a level the portable child inherits — e.g. user env); launch the exe; poll http://localhost:3001 for 200. Cloud SQL proxy is best-effort (needs gcloud ADC) and never blocks the window from opening.

Bigger point: a green Cloud Build (install → vitest → next build → electron-builder package → GCS upload) already proves the exe was built from code that passes tests and compiles; launching it only additionally checks runtime wiring, which needs the machine configured like a real operator's.
