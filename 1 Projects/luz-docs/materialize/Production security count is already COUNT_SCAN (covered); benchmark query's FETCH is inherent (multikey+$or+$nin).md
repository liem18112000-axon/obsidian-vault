---
title: "Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)"
aliases:
  - "_shard count: index bounds the slice but FETCHes every doc for un-indexed predicates"
created: 2026-06-17
type: observation
status: seedling
source: "LUZ-154613 session 2026-06-17, explain on dev (tenant 128k)"
tags: [luz-docs, mongodb, count, covered-index, explain, fetch, performance]
---

# Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)

**Production count is already optimal.** The real materialised count is the SECURITY query (`_effectiveSecurityClassCodes ∈ codes OR _isPublic`); its `$count` aggregate plan is `COUNT_SCAN,COUNT_SCAN` end-to-end on `idx_effectiveSecurityClassCodes_shard` — keys-only, ZERO FETCH. Fan-out just splits that keys-scan into K parallel `_shard` slices.

**The benchmark's slowness is its BODY, not the index.** explain of one K=16 `_shard` slice: winningPlan `IXSCAN idx_shard → FETCH`, `nReturned = keysExamined = docsExamined = 7993`, ~1.9s. The `_shard` range bounds candidates to exactly the slice (no over-scan), but the eArchive list filter (`$or(isStored, folderIds exists)` AND `$nin letterInfo.mediaType`) is not in the index, so Mongo FETCHes every candidate's full BSON to evaluate it. `docsExamined == keysExamined == nReturned` is the signature: perfect index bounds, still one document read per candidate. The FETCH — not the index scan — is the per-sub-count cost, which is exactly why fan-out yields ~1.8x (it divides FETCH-and-evaluate work by K).

That FETCH is **inherent**: `folderIds` is multikey plus `$or` plus `$nin`, and `partialFilterExpression` cannot express `$or`/`$nin`, so no covered or partial index rescues it.

**Ceiling** (~3.7s at K=16 vs ~1.9s single slice) is pod CPU cores / mongo-connection headroom — infra, not a luz-docs knob (no explicit rest-client pool config; `connectionPoolSize` isn't a standard MicroProfile property).

**Conclusion:** no luz-docs code change optimises this further for exact, no-cache counts. Remaining exact levers: more cores/connections (k8s), a covered compound index per hot query (predicate fields BEFORE `_shard` → keys-only COUNT_SCAN), or a Roaring bitmap (removes FETCH + amplification entirely, popcount with no per-doc read).

**Method lesson:** explain with `$count` (COUNT_SCAN) — never `find()`, which always FETCHes — to judge whether a count is covered.

## Related

- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
