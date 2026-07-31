---
title: "luz_docs safeCache is a deliberate per-class private copy"
created: 2026-07-20
type: lesson
status: seedling
source: "session 2026-07-20 LUZ-156314"
tags: [luz-docs, cache, convention, duplication]
---

# luz_docs safeCache is a deliberate per-class private copy

In luz_docs, `safeCache` — a tiny private helper that runs a cache get/put and swallows RuntimeException (returning null/default) — is intentionally **copied per class**, not extracted into a shared util. Copies live in VaultService, MigrationMessageReceiver, MigrationEventOrchestrator, and every feature gate (Materialize/Parallelize/Ngram).

Why: cache failure (Hazelcast down, network partition) must never block the guarded operation; worst case is a recompute or a fresh token. The duplication is documented as deliberate in `service/vault/DESIGN.md`.

Two exceptions where put() is deliberately NOT wrapped: the migration OVERLAPPING_GUARD lock — if the lock cannot be written, execution must halt rather than run unguarded (documented in `migration/DESIGN.md`).

How to apply: when adding a new gate/service that touches DualCache, copy the private `safeCache` in-class; do not create a shared CacheUtil, and do not wrap lock-acquisition puts.

Related: [[Campaign-gate template: cache then campaign status L1 then repository L2]]

## Related

- [[Campaign-gate template: cache then campaign status L1 then repository L2]]
