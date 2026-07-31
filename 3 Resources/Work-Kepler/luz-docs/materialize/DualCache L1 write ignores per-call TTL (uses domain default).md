---
title: "DualCache L1 write ignores per-call TTL (uses domain default)"
created: 2026-07-15
type: lesson
status: seedling
source: "luz_docs materialize-gate reliability research, 2026-07-15"
tags: [luz-docs, caching, gotcha, ttl]
---

# DualCache L1 write ignores per-call TTL (uses domain default)

DualCache (ch.klara.luz.docs.cache.DualCache) wraps an L1 in-process SimpleCache + an L2 distributed cache, but SimpleCache is built with ONE fixed TTL at construction time and DualCache.put(...) only forwards the caller's per-call ttlSeconds to L2 — the L1 .put(key,value) call has no TTL parameter, so L1 silently uses whatever fixed TTL that cache domain was built with, regardless of what the caller asked for.

Concretely: MaterializeCache's shared L1 domain is built with a fixed 300s TTL. MaterializeGate.writeCompletionStatusToCache passes a 60s TTL for the negative-cache (INCOMPLETE) case, intending a short window so a false negative gets re-checked soon. L2 honors 60s, but the writing pod's own L1 can keep serving the stale value for up to 300s — 5x longer than intended.

Every other DualCache consumer in the codebase has the same latent bug, just currently masked by secondary safety nets: MigrationEventOrchestrator.DEDUP_L1 (intends 24h) and MigrationMessageReceiver.OVERLAPPING_GUARD (intends 8h) both rely on this same shared mechanism.

Fix for a short/negative-cache TTL case: don't try to add per-entry TTL support to the shared SimpleCache/DualCache (bigger blast radius, touches unrelated consumers) — instead just skip the L1 tier for that specific write and always read through L2.

## Related

- [[Migration campaign status can silently drift from real document state]]
