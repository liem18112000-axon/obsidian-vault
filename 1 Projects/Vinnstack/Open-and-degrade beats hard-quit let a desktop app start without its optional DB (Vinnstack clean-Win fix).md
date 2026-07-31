---
title: "Open-and-degrade beats hard-quit: let a desktop app start without its optional DB (Vinnstack clean-Win fix)"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15"
tags: [vinnstack, electron, startup, resilience, clean-install]
---

# Open-and-degrade beats hard-quit: let a desktop app start without its optional DB (Vinnstack clean-Win fix)

Fix so the Vinnstack exe runs on a CLEAN Windows 10/11 (2026-07-15): the packaged app previously HARD-QUIT at startup when no DB was configured (electron/main.js startNextServer threw "No database connection string configured yet"), which on a fresh machine (no DATABASE_URL env, no config.json.databaseUrl) meant the window never opened — and it told you to "open Settings" while quitting, so Settings was unreachable. Fix: since lib/core/db.ts is LAZY (its pool() only connects on the first query), just skip setting DATABASE_URL when absent and open the app anyway; DB-backed features (Interrogation Room) surface a clear error when used, everything else (Chat, Skills, Notebook, Graphify, Ultracode, Polaris) works without a DB. Verified on a clean-machine sim (env -u DATABASE_URL, no config, no proxy): HTTP 200 + full startNextServer happy-path in the startup log.

Other clean-Win prerequisites are runtime/feature-level, NOT startup blockers, and degrade gracefully: cloud-sql-proxy is auto-started best-effort (needs gcloud ADC + the proxy binary; skipped if absent); the claude CLI is needed for AI chat/ultracode; gcloud + Cloud SQL for DB features. The window opens regardless.

Principle: a desktop app should OPEN and degrade, never hard-quit before the UI, on a missing optional backend — especially when the remedy (Settings) lives inside that same UI. Related: [[Vinnstack exe won't open without a pre-set databaseUrl (chicken-and-egg it says open Settings but quits first)]].
