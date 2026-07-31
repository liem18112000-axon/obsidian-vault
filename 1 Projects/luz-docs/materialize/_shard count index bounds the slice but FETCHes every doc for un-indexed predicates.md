---
title: "_shard count: index bounds the slice but FETCHes every doc for un-indexed predicates"
created: 2026-06-17
type: observation
status: seedling
source: "LUZ-154613 session 2026-06-17"
tags: [luz-docs, mongodb, index, count, explain, fetch, performance]
---

# _shard count: index bounds the slice but FETCHes every doc for un-indexed predicates

explain on dev (one K=16 _shard slice of the eArchive benchmark count, tenant 128k): winningPlan = IXSCAN idx_shard → FETCH; nReturned=keysExamined=docsExamined=7993, ~1.9s. So the _shard range bounds candidates to exactly the slice (NO over-scan), but Mongo then FETCHes every doc in the slice because the query's other predicates ( isStored/folderIds exists, $nin letterInfo.mediaType) are NOT in the index → must read each full doc to evaluate them. The FETCH (whole-BSON read), not the index scan, is the per-sub-count cost. This is exactly why fan-out gives ~1.8x: it divides the FETCH-and-evaluate work by K.

To cut count time further with EXACT counts and NO cache:
1. Covered compound index per hot query — predicate fields BEFORE _shard → keys-only COUNT_SCAN, no FETCH. Production count is the security query (_isPublic / _effectiveSecurityClassCodes∈codes); {_effectiveSecurityClassCodes:1,_shard:1}/{_isPublic:1,_shard:1} likely already cover it keys-only — VERIFY prod count avoids FETCH. The benchmark body only added un-indexed extras.
2. Partial index where a fixed filter holds (IXSCAN-only). Limit: partialFilterExpression can't express $or/$nin.
3. Connection pool >= K + more cores → raise the fan-out ceiling (K=8≈K=4 suggests connection contention).
4. Roaring bitmap (exact) — removes FETCH + amplification entirely (popcount, no per-doc read).

A Mongo count still FETCHes docs to evaluate any predicate not contained in the chosen index — only a fully covered index avoids it. docsExamined==keysExamined==nReturned means the index bounded candidates perfectly but every candidate was still fetched for predicate eval.

## Related

- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
