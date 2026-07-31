---
title: "Parallelize visible-doc count by fan-out over _id ranges (luz-docs)"
created: 2026-06-16
type: howto
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, materialize, mongodb, performance, objectid, fanout]
---

# Parallelize visible-doc count by fan-out over _id ranges (luz-docs)

luz-docs materialised visible-document count can be parallelised by **divide-and-conquer over the `_id` space**: split into K disjoint contiguous ObjectId ranges, run K `countByFilter` sub-counts concurrently (one per core via ManagedExecutorService), sum the partials. Sum is exact for any id distribution because the ranges tile the whole space with no gap/overlap.

Implementation decisions (LUZ-154613, package `ch.klara.luz.docs.materialize.parallelize`):
- **No Mongo change allowed** (Mongo is wrapped by the JsonStore gateway). So fan-out issues only ordinary match-style queries `{$and:[<base>, {_id:{$gte:'B(i-1)', $lt:'Bi'}}]}` through the existing `jsonStore.countByFilter`. Bounds are plain 24-char hex strings — the gateway already coerces `_id` string→ObjectId, so no $oid / extended-JSON wrapper needed.
- **Boundaries**: K-1 evenly spaced cuts = SPACE*i/K where SPACE = 2^96 (BigInteger.ONE.shiftLeft(96)), formatted as 24-char zero-padded lowercase hex. First range open below (no $gte), last open above (no $lt).
- **Wiring is one line**: `MaterializeRepository.countTotalDocumentByQuery` delegates to `MaterializeCountFanout.count(query, q -> jsonStore.countByFilter(...))`; the single count stays the per-range primitive passed as a `ToIntFunction<JsonObject>` lambda. Entry point above it (MaterializeFacade) is unchanged.
- **Fail-loud**: if any sub-count throws, the whole count fails (never returns a partial sum) — an under-count on a security-visibility total is an access-leak-shaped bug.
- **Config** `luz.docs.materialize.count-fanout-partitions`, default 1 = OFF (safe default). Injected with `@ConfigProperty(defaultValue="1") int`.
- **Speedup is conditional**: only helps when CPU-bound + spare cores + index resident, and only if the index has `_id` as the SECOND key (`{_effectiveSecurityClassCodes:1,_id:1}`, `{_isPublic:1,_id:1}`) so each range is an index seek not a post-filter. Otherwise it's K× work for zero gain. Load skews because ObjectIds cluster by creation time — correctness unaffected, only the speedup caps.

## Related

- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]
