---
title: "A per-checkpoint lastModified timestamp doubles as a liveness heartbeat for timeout detection"
created: 2026-08-07
type: lesson
status: seedling
source: "session 2026-08-07 luz_docs_import timeout work"
tags: [timeout, heartbeat, distributed-systems, job-processing, luz-docs]
---

# A per-checkpoint lastModified timestamp doubles as a liveness heartbeat for timeout detection

A job/record timestamp that is rewritten on **every progress checkpoint** (e.g. a batch flush every ≤100 items or ≤1s, where the same `updateJob` write stamps `lastModified = now`) doubles as a per-second **liveness heartbeat** — you get staleness detection for free, with no external liveness probe.

**When it applies:** long-running workers that persist incremental progress.

**Detection rule:** a job is stuck/dead when `now - lastModified > threshold` **AND** its status is non-terminal. The instant the worker dies or wedges, the heartbeat freezes, so the staleness grows without bound.

**Why prefer this over a pod/liveness probe:** no `kubectl get pod` shell-out from inside the container, no RBAC, no separate healthcheck endpoint — and it also catches a *hung* worker whose pod is still 'Running' but making no progress, which a pod-status probe misses.

Discovered in luz_docs_import: `JsonStoreService.updateJob` sets `lastModifield = Instant.now()` on every call, and the import worker flushes continuously via `JobProgressWriter`, so `lastModifield` is a live heartbeat.

See [[Read-time non-persisted state repair is an anti-pattern; use a scheduled reconciler]] for how to act on the stale signal correctly.

## Related

- [[Read-time non-persisted state repair is an anti-pattern; use a scheduled reconciler]]
