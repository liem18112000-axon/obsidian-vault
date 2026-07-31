---
ai_hash: 3e210574054e726b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack PRD inline-comments plan
status: seedling
tags:
- postgres
- foreign-keys
- cascade
- gotcha
- vinnstack
title: Delete-and-reinsert aggregate saves silently cascade-wipe new child tables
type: lesson
---

# Delete-and-reinsert aggregate saves silently cascade-wipe new child tables

When a persistence layer saves an aggregate by **deleting the parent row and re-inserting it** (a common simple-upsert pattern), any table you later attach with `REFERENCES parent(...) ON DELETE CASCADE` gets **silently wiped on every save** — the delete fires the cascade even though the parent is immediately re-inserted.

Two sharpening facts (confirmed 2026-07-03 against the vinnstack code):
- **CASCADE fires immediately at delete time, even inside a transaction.** `SET CONSTRAINTS ... DEFERRED` only defers constraint *checking*, never referential *actions* — so "delete then re-insert in the same tx" gives the cascade no chance to be skipped. There is no FK setting that survives a root delete-reinsert.
- **Blast radius = every code path that funnels through the aggregate save.** If all mutations go through one `save()` that deletes the root, then *any* unrelated edit (answering one question) wipes the new child table — not just edits to the entity the child hangs off.

Before hanging a new child table (e.g. comments) off an existing parent, check how the parent is written. If it's delete-and-reinsert, referencing "a more stable ancestor" only helps if that ancestor is truly never deleted — the real fix is:
1. change the save to **UPSERT the root row** (`INSERT ... ON CONFLICT DO UPDATE`) and explicitly delete only the child tables the aggregate owns, and
2. point the new child's FK at the now-stable root, keeping `ON DELETE CASCADE` for genuine entity deletion, and
3. add a regression test: save the aggregate twice, assert the new child rows survive.

Concrete case: vinnstack's `saveInterrogation` (lib/interrogationStore.ts L347–358) runs `DELETE FROM interrogations WHERE epic=$1` relying on cascade, then re-inserts everything; `recordAnswer`, `finalizeTrack`, `setPrd`, `recordPrdApproval` all funnel through it. A `prd_comments` FK to `prds(epic)` *or* `interrogations(epic)` would be wiped on every answer recorded, unless the save is changed as above.

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]
- [[Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)]]
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]

%% ai-graph-end %%