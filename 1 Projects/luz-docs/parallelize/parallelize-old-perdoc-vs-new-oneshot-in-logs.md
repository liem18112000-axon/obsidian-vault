---
title: Parallelize _shard migration — telling old per-doc vs new one-shot apart in logs
tags: [luz, parallelize, count-shard, migration, logs, jsonstore]
created: 2026-07-16
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
