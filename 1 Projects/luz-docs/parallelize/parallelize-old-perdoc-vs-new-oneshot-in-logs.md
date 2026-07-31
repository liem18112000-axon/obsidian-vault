---
ai_hash: 1b752d729c99fbeb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities:
- PARALLEL_DOCUMENT_COUNT_SHARDING
- JsonStoreLoggingFilter
- Old per-doc updateOne
- New one-shot updateMany
- luz-jsonstore
- luz_jsonstore/api/mdb/<tenant>/documents/<docId>
- _shard
- ParallelizeShardCompute.compute()
- ThreadLocalRandom
- luz-docs
- EE-ManagedExecutorService
- CompletableFuture.runAsync
- Semaphore BURST=3
- luz_jsonstore/api/mdb/<tenant>/documents
- $rand
- '@ApplicationScoped'
- luz-docs-batch
- matchedCount
- modifiedCount
- c1085cf9-052c-4098-bda7-b0b36518ef12
- cluster00
- 2026-07-15 09:11
- luz-docs-0
- '2026-07-16'
- gcloud-logging-shard-field-vs-sharding-keyword
tags:
- luz
- parallelize
- count-shard
- migration
- logs
- jsonstore
title: Parallelize _shard migration — telling old per-doc vs new one-shot apart in
  logs
type: resource
---

# Old per-doc updateOne vs new one-shot updateMany (parallelize sharding)

Two generations of the `PARALLEL_DOCUMENT_COUNT_SHARDING` migration exist. Distinguish them by the jsonstore `JsonStoreLoggingFilter` PATCH line:

## Old (per-document `updateOne`)
```
[PATCH] http://luz-jsonstore:8080/luz_jsonstore/api/mdb/<tenant>/documents/<docId>
        request-body={"$set":{"_shard":<int>}}
```
- One PATCH **per document** (path ends in `/documents/{id}`).
- `_shard` is a plain Java int from `ParallelizeShardCompute.compute()` (ThreadLocalRandom).
- Runs on `luz-docs` **main pods** (`luz-docs-0…`) via `EE-ManagedExecutorService` threads + `CompletableFuture.runAsync` (Semaphore BURST=3).

## New (one-shot `updateMany`)
```
[PATCH] http://luz-jsonstore:8080/luz_jsonstore/api/mdb/<tenant>/documents      <-- no /{id}
        request-body={"filter":{"_shard":{"$exists":false}},
                      "update":[{"$set":{"_shard":{"$toLong":{"$floor":{"$multiply":[{"$rand":{}}, 1073741824]}}}}}]}
```
- **Single** PATCH for the whole collection (path is `/documents`, no id).
- `_shard` computed per-document inside the aggregation pipeline via `$rand` (each doc distinct).
- Executor is `@ApplicationScoped`, runs in `luz-docs-batch`; reads `matchedCount`/`modifiedCount` from the response.

## Concrete observation (dev)
Tenant `c1085cf9-052c-4098-bda7-b0b36518ef12` (cluster00) was taken from 38,404/200,004 sharded to 200,004/200,004 by the **OLD per-doc path on 2026-07-15 09:11** (`luz-docs-0`), NOT the new one-shot. The new one-shot was deployed 2026-07-16, after the tenant was already fully sharded — so seeing 0 unsharded does NOT by itself prove the new code ran. Check the PATCH shape to know which generation did the work.

Related: [[gcloud-logging-shard-field-vs-sharding-keyword]].

%% ai-graph-start %%

**Related notes:**
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]
- [[gcloud-logging-shard-field-vs-sharding-keyword]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]

**Relations:**
- PARALLEL_DOCUMENT_COUNT_SHARDING — *has generation* — Old per-doc updateOne
- PARALLEL_DOCUMENT_COUNT_SHARDING — *has generation* — New one-shot updateMany
- Old per-doc updateOne — *distinguished by* — JsonStoreLoggingFilter
- New one-shot updateMany — *distinguished by* — JsonStoreLoggingFilter
- Old per-doc updateOne — *uses endpoint* — luz_jsonstore/api/mdb/<tenant>/documents/<docId>
- Old per-doc updateOne — *updates field* — _shard
- _shard — *computed by* — ParallelizeShardCompute.compute()
- ParallelizeShardCompute.compute() — *uses* — ThreadLocalRandom
- Old per-doc updateOne — *runs on* — luz-docs
- Old per-doc updateOne — *uses* — EE-ManagedExecutorService
- Old per-doc updateOne — *uses* — CompletableFuture.runAsync
- Old per-doc updateOne — *uses* — Semaphore BURST=3
- New one-shot updateMany — *uses endpoint* — luz_jsonstore/api/mdb/<tenant>/documents
- New one-shot updateMany — *updates field* — _shard
- _shard — *computed by* — $rand
- New one-shot updateMany — *runs in* — luz-docs-batch
- New one-shot updateMany — *uses executor* — @ApplicationScoped
- New one-shot updateMany — *reads from response* — matchedCount
- New one-shot updateMany — *reads from response* — modifiedCount
- c1085cf9-052c-4098-bda7-b0b36518ef12 — *is tenant in* — cluster00
- c1085cf9-052c-4098-bda7-b0b36518ef12 — *processed by* — Old per-doc updateOne
- Old per-doc updateOne — *ran on* — 2026-07-15 09:11
- Old per-doc updateOne — *ran on pod* — luz-docs-0
- New one-shot updateMany — *deployed on* — 2026-07-16
- gcloud-logging-shard-field-vs-sharding-keyword — *is related to* — PARALLEL_DOCUMENT_COUNT_SHARDING

%% ai-graph-end %%