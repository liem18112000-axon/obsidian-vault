---
ai_hash: e7646decddc991a7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: Vinnstack Graphify→Postgres 2026-07-03
status: seedling
tags:
- postgres
- persistence
- design-pattern
- jsonb
- nextjs
title: 'DB-first-with-file-fallback: opt-in Postgres persistence over a file cache'
type: howto
---

# DB-first-with-file-fallback: opt-in Postgres persistence over a file cache

A clean way to add Postgres persistence to a feature that already caches to local JSON files, without breaking the file-only path:

- **Opt-in on DATABASE_URL.** A `dbEnabled()` guard (`!!(process.env.DATABASE_URL ?? "").trim()`) gates all DB access. No DB configured → every DB function is skipped and the code behaves exactly as before (files only). This keeps local/offline dev working.
- **Best-effort ingest.** After the feature writes its files, ingest them into the DB in a try/catch that logs and swallows. A DB hiccup never fails the primary operation — the files remain the source of truth and fallback.
- **DB-first reads with file fallback.** Reads query the DB first; on a miss OR any error, fall back to reading the file. Resilient to a down/empty DB.
- **Store the whole artifact as a JSONB blob** (one row per entity) when the consumer reads it as one unit (e.g. a graph the frontend renders whole) — normalizing into relational tables is wasted effort there.

Gotcha: making a previously-synchronous read (`readX(): T`) DB-backed forces it async (`Promise<T>`); update every caller to `await`. `node-postgres` auto-parses JSONB columns into JS objects, so re-`JSON.stringify` if the HTTP layer expects a raw JSON string.

Applied in Vinnstack for Graphify code-graphs (lib/graphifyStore.ts), mirroring the earlier Interrogation Room JSON→Postgres migration.

## Related

- [[node-postgres percent-decodes the connection-string password]]

%% ai-graph-start %%

**Related notes:**
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)]]
- [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]
- [[Do not hardcode a real DB password as a source-code fallback for a packaged desktop app]]
- [[Auto-versioning generated files into a local git repo add -A + serialized queue + fire-and-forget]]

%% ai-graph-end %%