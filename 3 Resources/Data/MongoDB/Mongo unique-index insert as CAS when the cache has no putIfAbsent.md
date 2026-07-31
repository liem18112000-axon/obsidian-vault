---
ai_hash: c4f71920a091b841
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23 gate stampede discussion
status: seedling
tags:
- mongodb
- distributed-lock
- cas
- luz-docs
title: Mongo unique-index insert as CAS when the cache has no putIfAbsent
type: howto
---

# Mongo unique-index insert as CAS when the cache has no putIfAbsent

When the shared cache offers only get/put/delete (no putIfAbsent/CAS — e.g. luz_cache), a reliable distributed lock can still be built from **MongoDB unique-index rejection**: `insertOne` a lock document with a deterministic `_id` (e.g. the tenant id) into a lock collection — exactly one concurrent inserter succeeds, the rest get duplicate-key errors and know they lost. Release = delete after publishing the result; add a TTL index as the crash-recovery lease.

Gotchas:
- Get-then-insert (the `findOrCreate` shape, e.g. `MigrationCampaignService.findOrCreate` in luz_docs) is NOT atomic — two racers both see absent and both insert unless the unique index rejects the second. The atomicity lives in the index, never in the read-check.
- Through a REST facade (jsonstore) each acquire costs a network roundtrip and a per-tenant collection; fine for per-cold-window locks, wrong for per-request hot paths.
- The duplicate-key error IS the signal — catch it as the loser branch, do not treat it as a failure to retry.

## Related

- [[3 Resources/Backend/Concurrency/Lock-based stampede control losers hit the cache before the winner fills it]]

%% ai-graph-start %%

**Related notes:**
- [[Lock-based stampede control losers hit the cache before the winner fills it]]
- [[Shared aggregate write targets need CAS, not plain $set]]
- [[Per-pod single-flight kills cache stampede without semantic change]]
- [[Semaphore acquire before try leaks permits on static semaphores]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]

%% ai-graph-end %%