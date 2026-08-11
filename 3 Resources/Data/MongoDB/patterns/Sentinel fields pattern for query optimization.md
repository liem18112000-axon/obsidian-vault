---
ai_hash: 060faf7a2890dfda
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
status: seedling
tags:
- denormalization
- query-optimization
- mongodb
- materialize
title: Sentinel fields pattern for query optimization
type: pattern
---

# Sentinel fields pattern for query optimization

Stamp the read path's answer onto the document itself (an underscore-prefixed **sentinel field**) so the query needs no `$lookup`, no security-context evaluation, and no N+1 fetch. The cost moves to the write side, which must cascade the field whenever its source changes.

eArchive materialize sentinels on `documents`:
- `_isPublic` (bool) — replaces security-context evaluation
- `_effectiveSecurityClassCodes` (array) — replaces runtime access-control checks
- `_folderNames` (array) — replaces the per-doc `$lookup` on folders

**Use it when** reads dominate writes, the field is small and cheaply computable (not an aggregation), and brief staleness is tolerable.

**Non-negotiables:** index every sentinel (they are now the query filters); track cascade state (pending/in-progress/failed) so operators can detect and repair stale docs — see [[Cascade marker pattern for bulk update resilience]]; decide synchronous-on-write (write latency) vs job-based (window of inconsistency) explicitly.

## Related

- [[Cascade marker pattern for bulk update resilience]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[Materialize feature architecture]]

%% ai-graph-start %%

**Related notes:**
- [[Don't share one predicate between a read-path gate and a backfill selector]]
- [[Cascade marker pattern for bulk update resilience]]
- [[Parallel arrays in materialize sentinel preserve folderId order]]
- [[Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[Materialize gate must require _shard or parallelized count undercounts]]

%% ai-graph-end %%