---
title: "Vinnstack exe won't open without a pre-set databaseUrl (chicken-and-egg: it says open Settings but quits first)"
created: 2026-07-15
type: observation
status: seedling
source: "session 2026-07-15"
tags: [vinnstack, electron, startup, ux-bug, database]
---

# Vinnstack exe won't open without a pre-set databaseUrl (chicken-and-egg: it says open Settings but quits first)

Vinnstack packaged-exe UX bug (2026-07-15): electron/main.js startNextServer() THROWS "No database connection string configured yet. Open Settings and set the database connection (Advanced), then relaunch." when neither config.json.databaseUrl nor process.env.DATABASE_URL is set. The throw hits the whenReady catch -> fail() dialog -> app.quit(). Chicken-and-egg: it tells the user to open Settings, but the app has already quit, so Settings is unreachable — a first-run user on a clean machine (no config, no ambient DATABASE_URL) can never get in. Confirmed via the startup log (fresh-user sim with `env -u DATABASE_URL`): "startup failed: No database connection string..." at main.js:183.

The cloud-sql-proxy is NOT the blocker — the exe auto-starts it (best-effort; needs gcloud ADC + cloud-sql-proxy on PATH) and continues without it if unavailable. The DB is also LAZY (lib/db.ts connects on demand), so the hard startup gate is unnecessary: dropping the throw would let the window open and let DB-backed features (Interrogation Room) degrade to a clear error while the user configures the DB in Settings.

Proposed fix: in startNextServer, don't throw on missing databaseUrl — just skip setting process.env.DATABASE_URL (or set nothing) and let the app open; surface "configure the database in Settings" in-app instead of quitting. Then first-run works without any pre-seeded config.

Testing gotcha that hid this: an ambient DATABASE_URL in the shell made the gate pass in every launch — always test first-run with `env -u DATABASE_URL`. Related: [[ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing]].
