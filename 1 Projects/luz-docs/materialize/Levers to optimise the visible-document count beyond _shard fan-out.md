---
title: "Levers to optimise the visible-document count beyond _shard fan-out"
created: 2026-06-17
type: argument
status: seedling
source: "LUZ-154613 session 2026-06-17"
tags: [luz-docs, performance, count, caching, roaring, index]
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

- [[Dev benchmark: _shard count fan-out ~1.8x]]
- [[diminishing past K=12; local port-forward hid the gain]]
- [[Roaring bitmaps for exact visible-document count removes the doc-times-code amplification]]
