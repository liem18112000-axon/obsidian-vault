---
title: "luz_docs stamps _shard on create to keep sharding gate stable"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs commit ee4bf5cad, code review 2026-07-09/10"
tags: [luz-docs, parallelize, design-decision, write-path, cache-invariant]
---

# luz_docs stamps _shard on create to keep sharding gate stable

A cached 'gate' that asserts some invariant holds (e.g. 'every document in this tenant has field X') is only sound if the **write path** actually maintains that invariant going forward — a read-side cache with a TTL can only bound the blast radius of temporary staleness, it cannot substitute for a write-side guarantee that was never established.

In luz_docs, a count-fan-out optimization only activates once every document in a tenant has been backfilled with a `_shard` field (checked by `ParallelizeGate.isShardingComplete`, cached for up to an hour once true). The one-time backfill migration stamped `_shard` on existing documents, but nothing on the *document-creation* path did — so every document created after the backfill completed was invisibly missing `_shard` again, silently re-breaking the 'complete' invariant the cached gate still believed was true, and causing undercounted results for up to an hour at a time, recurring forever on every future create.

The fix: add `ParallelizeFacade.stampShardOnCreate(metadata)`, called from both document-creation entry points right before the actual persistence call — mirroring the exact pattern an earlier, similar feature (`materializeFacade.stampMaterializeOnCreate`) already used two lines above it in the same method. Once the write path itself guarantees the field, 'complete' becomes a true one-way invariant, and the cache TTL becomes a pure performance knob instead of a correctness gamble on staleness.

**Generalizable check:** whenever you find a cached gate/flag that says 'condition X holds for all records', trace *every* write path that can create or mutate a record of that kind — not just the migration/backfill job that historically established the condition. If even one write path can produce a record violating X, the cache is only ever safe for a TTL window, not durably true.

## Related

- [[Two-tier cache must propagate caller TTL to every tier]]
