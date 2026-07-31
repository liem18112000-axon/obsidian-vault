---
ai_hash: 76348f710ca3a2fb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
tags:
- luz
- count-shard
- gcloud
- logging
- gotcha
- filter
title: gcloud log filter — "_shard" matches the count-fanout read path, not the migration
type: resource
---

# Filtering luz count-shard logs: `_shard` ≠ `sharding`

When pulling "count shard migration" logs for a tenant, a regex on **`_shard` / `shard`** floods and hits the `gcloud logging read --limit` cap (5000) with pure noise:

- The **count read path** logs one `POST luz-jsonstore/.../documents/count` per partition, each body ending in a `_shard` range predicate, e.g. `{"_shard":{"$gte":89478485,"$lt":178956970}}` or `{"_shard":{"$exists":false}}`. Dozens per count call → thousands of lines. This is **runtime**, not the migration.

Use a keyword that only matches migration/feature identifiers, **not** the Mongo field:
```
textPayload=~"(?i)(sharding|parallelize|reshard)"
  OR textPayload:"MigrationMessageReceiver"
  OR textPayload:"ContextNotActive"
```
`sharding` matches `PARALLEL_DOCUMENT_COUNT_SHARDING` and `..._parallelize_sharding_complete` but NOT `{"_shard":...}`. Dropped 5000 capped noise → 182 signal lines.

## Where count-shard logs actually live
- **`luz-docs-batch-*`** — the campaign **executor** (`MigrationMessageReceiver [migration] starting campaign=PARALLEL_DOCUMENT_COUNT_SHARDING`). This is the only place `_shard` gets stamped.
- **`luz-docs-0..N`** (main) — read-path + **scheduling** only: `luz_docs_parallelize_sharding_complete` gate (`GET 404 → PUT` loop = gate never latches complete) and `luz_docs_migration_campaign_PARALLEL_DOCUMENT_COUNT_SHARDING_scheduled`.

Filter both by `resource.labels.container_name="luz-docs"`; split batch vs main with
`resource.labels.pod_name:"luz-docs-batch"` (or `... AND NOT ...:"luz-docs-batch"`).

Related: [[earchive-seed-stale-27017-portforward-gotcha]]; shard-gate-strict-write-path-gap.

%% ai-graph-start %%

**Related notes:**
- [[parallelize-old-perdoc-vs-new-oneshot-in-logs]]
- [[Don't share one predicate between a read-path gate and a backfill selector]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]

%% ai-graph-end %%