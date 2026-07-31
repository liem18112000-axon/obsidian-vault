---
title: "Materialize gate must require _shard or parallelized count undercounts"
created: 2026-06-19
type: lesson
status: seedling
source: "session 2026-06-19 LUZ-154613"
tags: [luz-docs, materialize, mongodb, gotcha, sharding]
---

# Materialize gate must require _shard or parallelized count undercounts

The eArchive parallelized count tiles `[0, SHARD_SPACE)` into K disjoint `_shard` range sub-counts (`ParallelizePartitioner`). A doc with no `_shard` matches **no** range, so it is silently dropped from the total — the count comes back short, not wrong-by-error. So the materialize gate (`MaterializeRepository.isMaterialized` → `buildUnmaterializedFilter`) must treat a missing `_shard` as *unmaterialized*, alongside the three read fields `_isPublic` / `_effectiveSecurityClassCodes` / `_folderNames`.

**Why it broke:** a doc stamped by an older version had the 3 read fields but no `_shard`. The gate (which lacked the `_shard` check) read the tenant as fully materialized and opened fan-out before every doc was stamped → undercount.

**Fix:** add `missingField(_shard)` to `buildUnmaterializedFilter`. The gate now returns INCOMPLETE while any doc lacks a shard, falling back to the safe non-parallel count until the backfill stamps everyone.

Related: [[Fan-out gate and backfill filter must cover the same field set]]

## Related

- [[Fan-out gate and backfill filter must cover the same field set]]
