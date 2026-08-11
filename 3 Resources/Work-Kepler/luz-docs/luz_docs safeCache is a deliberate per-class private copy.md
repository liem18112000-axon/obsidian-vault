---
ai_hash: bb09608d317b9baa
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: session 2026-07-20 LUZ-156314
status: seedling
tags:
- luz-docs
- cache
- convention
- duplication
title: luz_docs safeCache is a deliberate per-class private copy
type: lesson
---

# luz_docs safeCache is a deliberate per-class private copy

In luz_docs, `safeCache` — a tiny private helper that runs a cache get/put and swallows RuntimeException (returning null/default) — is intentionally **copied per class**, not extracted into a shared util. Copies live in VaultService, MigrationMessageReceiver, MigrationEventOrchestrator, and every feature gate (Materialize/Parallelize/Ngram).

Why: cache failure (Hazelcast down, network partition) must never block the guarded operation; worst case is a recompute or a fresh token. The duplication is documented as deliberate in `service/vault/DESIGN.md`.

Two exceptions where put() is deliberately NOT wrapped: the migration OVERLAPPING_GUARD lock — if the lock cannot be written, execution must halt rather than run unguarded (documented in `migration/DESIGN.md`).

How to apply: when adding a new gate/service that touches DualCache, copy the private `safeCache` in-class; do not create a shared CacheUtil, and do not wrap lock-acquisition puts.

Related: [[1 Projects/luz-docs/search/Campaign-gate template cache then campaign status L1 then repository L2]]

## Related

- [[1 Projects/luz-docs/search/Campaign-gate template cache then campaign status L1 then repository L2]]

%% ai-graph-start %%

**Related notes:**
- [[Campaign-gate template cache then campaign status L1 then repository L2]]
- [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]
- [[Mongo unique-index insert as CAS when the cache has no putIfAbsent]]

%% ai-graph-end %%