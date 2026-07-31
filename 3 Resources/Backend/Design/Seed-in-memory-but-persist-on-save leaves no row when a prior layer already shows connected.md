---
title: "Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected"
created: 2026-07-04
type: gotcha
status: seedling
source: "session 2026-07-04, vinnstack account_credentials empty"
tags: [persistence, seeding, ux, vinnstack]
---

# Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected

A "seed on read from a fallback layer, but only PERSIST on an explicit save" design has a dead zone: if the fallback layer already satisfies the UI, the save is never triggered, so the durable row is never written. In vinnstack: GET /api/account/credentials seeded the per-account form from the MACHINE config in memory (setActiveCredentials, no DB write); because the machine config already had Jira creds, the connection card showed "connected", so the user never re-entered + submitted credentials, so the mirror-on-save never ran, so account_credentials stayed empty forever. The user (correctly) reported "I'm logged in and it's connected but nothing saved."

The tell that isolated it: logs showed the session resolving fine (active account set with the real accountId) but ONLY GET calls — no credential POST, no mirror line. Session was never the bug; the missing write was.

Fix: on first read with no row, if the fallback layer has real data, PERSIST the seed (create the row) rather than only holding it in memory. Migrate-on-first-touch, don't wait for a save the UI will never prompt.

## Related

- [[Per-account write silently skipped when the server cant resolve the session looks saved]]
- [[isnt]]
