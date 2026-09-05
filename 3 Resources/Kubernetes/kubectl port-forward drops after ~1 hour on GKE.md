---
title: "kubectl port-forward drops after ~1 hour on GKE"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 (perf-cluster 2.2M seed)"
tags: [kubernetes, kubectl, port-forward, gke, gotcha, ops]
---

# kubectl port-forward drops after ~1 hour on GKE

A bare `kubectl port-forward` used by a long-running job (data seed, migration, load test, DB backfill) reliably drops after roughly **60 minutes** on GKE, because the apiserver caps the lifetime of streamed/SPDY connections. The failure surfaces to the client as `kubectl] error: lost connection to pod` followed by the app-side `ECONNREFUSED 127.0.0.1:<port>` (or `::1:<port>`), and **most clients do not auto-reconnect** — the job simply dies mid-run.

**When it applies:** any job that holds a single port-forward open for more than ~45 min. Observed 2026-09-03 on `klara-performance` seeding 2.2M Mongo docs — the forward dropped at ~3627s (~60 min) and the Node generator exited 1.

**What to do:** wrap the work in a supervisor that re-establishes the port-forward and resumes (see [[Resume a large append seed by recounting to a target]]). Short forwards (a quick count, a single request) are fine; long ones are not.

**Corollary gotcha — exit 0 can lie.** A background wrapper that reports `exit 0` may be masking an inner failure: if the outer scripts cleanup step returns 0, that becomes the wrappers exit code even though the real worker exited 1. Never trust the exit code alone for a long job — verify the actual end-state (row/doc count, output file, DB state) before declaring success.

## Related

- [[Resume a large append seed by recounting to a target]]
