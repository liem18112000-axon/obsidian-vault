---
ai_hash: 2cbf70db156d6666
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities:
- visible-document count
- _shard fan-out
- CACHE
- luz_cache
- tenant
- code-set
- cascade hooks
- BITMAP UNION
- Roaring exact
- HLL approx
- per-code bitmaps
- doc-id↔int map
- COUNT_SCAN
- FETCH
- explain
- gateway query
- folderIds
- mediaType
- index
- CONNECTION POOL
- K concurrent sub-counts
- jsonstore
- Mongo
- READ-REPLICA routing
- read-preference knob
- frozen gateway
- Bigger pod
- more cores
- exact totals
- UI
- approximate totals
- Dev benchmark _shard count fan-out project
- Visible-document count as cardinality of a bitmap union resource
- Act order
source: LUZ-154613 session 2026-06-17
status: seedling
tags:
- luz-docs
- performance
- count
- caching
- roaring
- index
title: Levers to optimise the visible-document count beyond _shard fan-out
type: argument
---

# Levers to optimise the visible-document count beyond _shard fan-out

Levers to cut the materialised visible-document count time beyond the _shard fan-out (which capped at ~1.8x, ceiling = cores). Ranked:

1. CACHE the count — the total for (tenant, code-set) only changes on create/delete/security-change. Cache in luz_cache keyed by sorted code-set+tenant, invalidate in existing cascade hooks. Repeat counts ~0ms; fan-out only helps a cache miss. Cheap, days.
2. BITMAP UNION (Roaring exact / HLL approx) — removes the O(doc×code) amplification entirely; count = popcount(OR of per-code bitmaps), sub-ms regardless of K. Biggest absolute win; weeks (maintain per-code bitmaps on writes + doc-id↔int map).
3. CONFIRM covered COUNT_SCAN not FETCH — 24s cold / 4s warm suggests sub-counts FETCH docs. Run explain on the actual gateway query; extra predicates (folderIds exists, mediaType not-in) may force FETCH/$expr. Add an index covering them beside _shard → keys-only count. Cheapest high-value check, do FIRST.
4. CONNECTION POOL >= K — K concurrent sub-counts need K jsonstore/Mongo connections; if pool<K they queue and fan-out silently serialises (likely why K=8≈K=4). Verify pool scales with K.
5. READ-REPLICA routing for heavy sub-counts — needs a read-preference knob on the frozen gateway.
6. Bigger pod / more cores — raises the fan-out ceiling, linear/finite.
7. DON'T compute exact totals (product lever) — UI usually needs 'has more', cap at '999+' → instant. Biggest win if requirement allows.

Act order: (3) explain → (1) cache → (4) pool → then (2) bitmap vs (7) approximate for the tail.

## Related

- [[1 Projects/luz-docs/materialize/Dev benchmark _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain]]
- [[3 Resources/Work-Kepler/luz-docs/count-optimize/Visible-document count as cardinality of a bitmap union]]

%% ai-graph-start %%

**Related notes:**
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Visible-document count as cardinality of a bitmap union]]
- [[Frozen JsonStore gateway makes _id-range count fan-out a dead end — pivot to bitmapHLL]]

**Relations:**
- visible-document count — *optimized beyond* — _shard fan-out
- _shard fan-out — *capped at* — ~1.8x
- CACHE — *optimizes* — visible-document count
- CACHE — *uses* — luz_cache
- luz_cache — *keyed by* — tenant
- luz_cache — *keyed by* — code-set
- CACHE — *invalidated by* — cascade hooks
- BITMAP UNION — *optimizes* — visible-document count
- BITMAP UNION — *is a type of* — Roaring exact
- BITMAP UNION — *is a type of* — HLL approx
- BITMAP UNION — *requires* — per-code bitmaps
- BITMAP UNION — *requires* — doc-id↔int map
- COUNT_SCAN — *preferred over* — FETCH
- explain — *analyzes* — gateway query
- folderIds — *can force* — FETCH
- mediaType — *can force* — FETCH
- index — *enables* — COUNT_SCAN
- CONNECTION POOL — *optimizes* — visible-document count
- CONNECTION POOL — *supports* — K concurrent sub-counts
- K concurrent sub-counts — *needs* — jsonstore
- K concurrent sub-counts — *needs* — Mongo
- READ-REPLICA routing — *optimizes* — visible-document count
- READ-REPLICA routing — *needs* — read-preference knob
- READ-REPLICA routing — *on* — frozen gateway
- Bigger pod — *optimizes* — visible-document count
- more cores — *optimizes* — visible-document count
- Bigger pod — *raises* — _shard fan-out
- more cores — *raises* — _shard fan-out
- approximate totals — *optimizes* — visible-document count
- UI — *uses* — approximate totals
- UI — *caps at* — '999+'
- Dev benchmark _shard count fan-out project — *related to* — _shard fan-out
- Visible-document count as cardinality of a bitmap union resource — *related to* — BITMAP UNION
- explain — *is first step in* — Act order
- CACHE — *is second step in* — Act order
- CONNECTION POOL — *is third step in* — Act order
- BITMAP UNION — *is fourth step in* — Act order
- approximate totals — *is fourth step in* — Act order

%% ai-graph-end %%