---
ai_hash: 1b722fbbb1ab05d8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities: []
source: luz_docs parallelize code review, 2026-07-09/10
status: seedling
tags:
- java
- concurrency
- semaphore
- resource-leak
- gotcha
- code-review
title: Semaphore acquire before try leaks permits on static semaphores
type: lesson
---

# Semaphore acquire before try leaks permits on static semaphores

Calling `Semaphore.acquireUninterruptibly()` (or any blocking acquire) before entering the `try` block that guarantees its `release()` creates a permanent leak risk the moment any fallible statement sits between the acquire and the try.

If that in-between statement throws — e.g. extracting a field from a JSON object that turns out malformed — the exception propagates past the acquire with no `finally` yet in scope to release the permit. The permit is gone for good.

This is low-severity when the semaphore is scoped to a single request/thread (the leak dies with that scope). It becomes a real production hazard when the semaphore is `static final` — shared across every caller, tenant, and invocation for the entire JVM's lifetime. Leaks then accumulate irreversibly across otherwise-unrelated operations. With a small permit count (e.g. 3), it doesn't take many rare/low-probability failures — across the whole process lifetime, not just one call — before the pool hits zero and every future `acquireUninterruptibly()` blocks forever (uninterruptible, no timeout), silently deadlocking the feature until process restart.

**Rule of thumb:** the acquire and its corresponding try/finally release must be adjacent, with zero fallible statements in between. If you must do fallible work first, either do it before acquiring the permit, or move it inside the try.

Found in luz_docs' `ParallelizeMigrationExecutor`: `BURST.acquireUninterruptibly(); var id = doc.getString(ID); try { ... } finally { BURST.release(); }` — the id extraction sits between acquire and try.

## Related

- [[jsonstore $in vs $nin ObjectId conversion gap]]

%% ai-graph-start %%

**Related notes:**
- [[Semaphore permit leak when risky code sits between acquire and try]]
- [[Static shared resources leak state across unrelated call sites]]
- [[Mongo unique-index insert as CAS when the cache has no putIfAbsent]]
- [[Per-pod single-flight kills cache stampede without semantic change]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]

%% ai-graph-end %%