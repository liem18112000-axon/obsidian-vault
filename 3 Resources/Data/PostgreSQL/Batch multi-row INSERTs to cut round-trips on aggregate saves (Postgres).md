---
ai_hash: cc3928205a17bc50
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities: []
source: session 2026-07-02
status: seedling
tags:
- postgres
- performance
- pg
- latency
- vinnstack
title: Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)
type: lesson
---

# Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)

When a store loads/saves a whole aggregate to Postgres via a delete-then-reinsert transaction, the naive port does one INSERT per child row — for a Vinnstack interrogation (~14 questions + options + answers + visuals + stories) that is ~80 sequential round-trips per save. Over a high-latency link this is brutal: e.g. a dev in Vietnam reaching a europe-west6 Cloud SQL instance through the Auth Proxy has ~250ms RTT, so 80 round-trips ≈ 20s per save (tests timed out at 5s, then even 30s).

Fix: **one batched multi-row INSERT per table** — build `INSERT INTO t (cols) VALUES ($1,$2,..),($n,..)` with a flat param array (a small `bulkInsert(client, table, cols, rows)` helper). Collapses ~80 round-trips to ~10 (one per table + BEGIN/DELETE/COMMIT). Postgres caps a statement at 65535 params — orders of magnitude above any real record.

Two gotchas learned:
- A single pg client/connection CANNOT run queries in parallel — `Promise.all` of queries on one client just serializes them on that connection. So batching (fewer statements) is the lever inside a transaction, not concurrency. (Parallel reads via the POOL are fine — different connections — which is why getInterrogation uses Promise.all across 8 read queries = ~1 RTT.)
- The latency is largely a LOCAL-DEV artifact: in prod the Next.js server runs co-located with the DB (~1ms RTT), so even the naive version would be fine. Batching still matters and is cheap, but do not over-optimize for a latency prod will not have.

Applied 2026-07-02 in lib/interrogationStore.ts writeAggregate.

## Related

- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]

%% ai-graph-start %%

**Related notes:**
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Delete-and-reinsert aggregate saves silently cascade-wipe new child tables]]
- [[DB-first-with-file-fallback opt-in Postgres persistence over a file cache]]

%% ai-graph-end %%