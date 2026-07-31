---
ai_hash: 791194473cb5569f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- luz-docs
- mongodb
- count
- materialize
- gotcha
title: luz-docs parallelized count undercounts documents missing _shard
type: lesson
---

# luz-docs parallelized count undercounts documents missing _shard

The luz-docs document **count** can fan out into K disjoint sub-queries over the `_shard` field (`ParallelizeCount` / `ParallelizePartitioner`). The K ranges tile `[0, SHARD_SPACE)` with the first range open-below and the last open-above, so together they cover every numeric `_shard` value.

**Gotcha:** MongoDB range predicates (`$lt`/`$gte`) do **not** match documents where the field is absent. So a document with **no `_shard` field** matches *no* chunk and is **silently undercounted** whenever fan-out is enabled. Un-stamped docs arise when they were created before the materialize backfill, or seeded directly into Mongo.

Key facts:
- `_shard` is stamped during materialize (`MaterializeCompute`, random in `[0, SHARD_SPACE)`, `SHARD_SPACE = 1<<30`), and on create only if `materializeFacade.shouldAllowedTenant(tenant)` is true.
- Fan-out is gated by config `luz.docs.materialize.count-fanout-partitions` (default `1` = disabled, clamped max 64). When disabled it runs a single plain count that always includes un-stamped docs.
- Fail-soft: if any sub-count throws, it falls back to a single count on the base query.
- Therefore: only enable fan-out after the backfill has stamped every doc.

Count endpoint: `POST /{tenant}/documents/count`; response field is `totalRecordCount`.

Related: [[Black-box test missing-_shard count correctness with a delta test]]

## Related

- [[Black-box test missing-_shard count correctness with a delta test]]

%% ai-graph-start %%

**Related notes:**
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]
- [[Black-box test missing-_shard count correctness with a delta test]]
- [[No existing luz-docs field works as a fan-out count partition key — survey]]
- [[Fan-out gate and backfill filter must cover the same field set]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]

%% ai-graph-end %%