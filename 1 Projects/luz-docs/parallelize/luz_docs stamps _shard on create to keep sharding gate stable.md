---
ai_hash: 0062e1d9c5e50a92
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities:
- luz_docs
- _shard field
- sharding gate
- cached gate
- invariant
- write path
- read-side cache
- TTL
- count-fan-out optimization
- ParallelizeGate.isShardingComplete
- backfill migration
- document-creation path
- undercounted results
- ParallelizeFacade.stampShardOnCreate
- materializeFacade.stampMaterializeOnCreate
- Two-tier cache must propagate caller TTL to every tier
- temporary staleness
- write-side guarantee
- performance knob
- document-creation entry points
source: luz_docs commit ee4bf5cad, code review 2026-07-09/10
status: seedling
tags:
- luz-docs
- parallelize
- design-decision
- write-path
- cache-invariant
title: luz_docs stamps _shard on create to keep sharding gate stable
type: lesson
---

# luz_docs stamps _shard on create to keep sharding gate stable

A cached 'gate' that asserts some invariant holds (e.g. 'every document in this tenant has field X') is only sound if the **write path** actually maintains that invariant going forward — a read-side cache with a TTL can only bound the blast radius of temporary staleness, it cannot substitute for a write-side guarantee that was never established.

In luz_docs, a count-fan-out optimization only activates once every document in a tenant has been backfilled with a `_shard` field (checked by `ParallelizeGate.isShardingComplete`, cached for up to an hour once true). The one-time backfill migration stamped `_shard` on existing documents, but nothing on the *document-creation* path did — so every document created after the backfill completed was invisibly missing `_shard` again, silently re-breaking the 'complete' invariant the cached gate still believed was true, and causing undercounted results for up to an hour at a time, recurring forever on every future create.

The fix: add `ParallelizeFacade.stampShardOnCreate(metadata)`, called from both document-creation entry points right before the actual persistence call — mirroring the exact pattern an earlier, similar feature (`materializeFacade.stampMaterializeOnCreate`) already used two lines above it in the same method. Once the write path itself guarantees the field, 'complete' becomes a true one-way invariant, and the cache TTL becomes a pure performance knob instead of a correctness gamble on staleness.

**Generalizable check:** whenever you find a cached gate/flag that says 'condition X holds for all records', trace *every* write path that can create or mutate a record of that kind — not just the migration/backfill job that historically established the condition. If even one write path can produce a record violating X, the cache is only ever safe for a TTL window, not durably true.

## Related

- [[Two-tier cache must propagate caller TTL to every tier]]

%% ai-graph-start %%

**Related notes:**
- [[Fan-out gate and backfill filter must cover the same field set]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[Don't share one predicate between a read-path gate and a backfill selector]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]
- [[luz_docs ParallelizeGate needs two indexes on _shard]]

**Relations:**
- luz_docs — *stamps* — _shard field
- _shard field — *stabilizes* — sharding gate
- cached gate — *asserts* — invariant
- write path — *maintains* — invariant
- read-side cache — *uses* — TTL
- read-side cache — *bounds* — temporary staleness
- read-side cache — *cannot substitute for* — write-side guarantee
- count-fan-out optimization — *requires* — _shard field
- ParallelizeGate.isShardingComplete — *checks for* — _shard field
- backfill migration — *stamped* — _shard field
- document-creation path — *failed to stamp* — _shard field
- missing _shard field — *caused* — undercounted results
- ParallelizeFacade.stampShardOnCreate — *adds* — _shard field
- document-creation entry points — *call* — ParallelizeFacade.stampShardOnCreate
- ParallelizeFacade.stampShardOnCreate — *mirrors* — materializeFacade.stampMaterializeOnCreate
- write path — *guarantees* — _shard field
- cache TTL — *becomes* — performance knob
- cached gate — *requires validation by* — write path
- luz_docs — *is related to* — Two-tier cache must propagate caller TTL to every tier

%% ai-graph-end %%