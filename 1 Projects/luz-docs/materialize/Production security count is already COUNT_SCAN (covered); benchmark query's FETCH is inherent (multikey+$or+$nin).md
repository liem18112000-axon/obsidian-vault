---
title: "Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)"
created: 2026-06-17
type: observation
status: seedling
source: "LUZ-154613 session 2026-06-17"
tags: [luz-docs, mongodb, count, covered-index, explain, performance]
---

# Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)

Diagnostic on dev (LUZ-154613): the production materialised count is the SECURITY query (_effectiveSecurityClassCodes∈codes OR _isPublic) — its $count aggregate plan is COUNT_SCAN,COUNT_SCAN end-to-end on idx_effectiveSecurityClassCodes_shard = keys-only, ZERO FETCH = already optimal. Fan-out splits that keys-scan into K parallel _shard slices.

The earlier ~4s benchmark slowness was the benchmark BODY (eArchive list filter: $or(isStored,folderIds exists) AND $nin letterInfo.mediaType), which is NOT coverable — folderIds is multikey (array) + $or + $nin force a FETCH of every candidate to evaluate the predicates (docsExamined==keysExamined==nReturned: index bounds the slice perfectly, but each doc is still fetched for predicate eval). partialFilterExpression can't express $or/$nin, so no partial-index rescue.

The K speedup ceiling (~3.7s vs ~1.9s single-slice) is parallelism-bound by the dev pod CPU cores / mongo-connection headroom — INFRA, not a luz-docs code knob (no explicit rest-client pool config; connectionPoolSize isn't a standard MicroProfile property).

Conclusion: no luz-docs code change optimises this further for exact, no-cache counts — the real count is already covered; the benchmark query's FETCH is inherent; the ceiling is pod resources. Remaining exact levers: more cores/connections (k8s), or Roaring bitmap (removes FETCH+amplification). Lesson: explain with $count (COUNT_SCAN) not find() (always FETCHes) to judge whether a count is covered.

## Related

- [[_shard count: index bounds the slice but FETCHes every doc for un-indexed predicates]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
