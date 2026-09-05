---
title: "Read-time non-persisted state repair is an anti-pattern; use a scheduled reconciler"
created: 2026-08-07
type: lesson
status: seedling
source: "session 2026-08-07 luz_docs_import timeout work"
tags: [anti-pattern, reconciler, idempotency, distributed-systems, luz-docs]
---

# Read-time non-persisted state repair is an anti-pattern; use a scheduled reconciler

Repairing durable state **inside a GET/poll handler** (e.g. flipping a stuck job to FAILED/TIMEOUT when someone fetches it) is a trap with three failure modes:

1. **Poll-dependent** — the repair runs only if a client polls that exact id. A job nobody polls stays stuck forever.
2. **Not persisted** — the handler mutates the in-memory object it returns but never writes it back, so the repair is recomputed on every read and lost each time.
3. **No side-effects** — no notification / downstream event fires, because those live on the normal completion path that never ran.

**Fix:** move the transition to a **scheduled reconciler/sweeper** that runs independently of reads, **persists** the terminal state, and fires the notification **once**. Under multiple replicas, guard the flip with an **optimistic compare-and-set** (only transition if still non-terminal) so exactly one replica acts and the notification isn't double-sent.

Surfaced in luz_docs_import: the old `DocsImportService.getImportJob` did exactly this (returned a FAILED/TIMEOUT flip that was never saved and never notified). Detect staleness via [[A per-checkpoint lastModified timestamp doubles as a liveness heartbeat for timeout detection]].

## Related

- [[A per-checkpoint lastModified timestamp doubles as a liveness heartbeat for timeout detection]]
