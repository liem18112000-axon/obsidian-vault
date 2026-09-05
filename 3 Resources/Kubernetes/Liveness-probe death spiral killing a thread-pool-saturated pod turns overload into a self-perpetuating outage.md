---
title: "Liveness-probe death spiral: killing a thread-pool-saturated pod turns overload into a self-perpetuating outage"
created: 2026-08-24
type: concept
status: seedling
source: "session 2026-08-24"
tags: [kubernetes, liveness-probe, thread-pool, resilience, gotcha, exit-137]
---

# Liveness-probe death spiral: killing a thread-pool-saturated pod turns overload into a self-perpetuating outage

When a service holds a request-worker thread for the whole duration of slow synchronous work, a burst of concurrency can exhaust the HTTP worker pool. If the **liveness probe is served by that same pool**, the probe stops answering, the kubelet declares the pod dead and **SIGKILLs it (exit 137)**. The restart drops all in-flight work and the fresh pod is instantly re-saturated — so the outage **perpetuates itself** instead of recovering. Killing a busy-but-healthy pod is the worst response to overload.

**Tell-tale signs:** exit code 137 with events `Liveness probe failed: ... context deadline exceeded` (NOT `OOMKilled`); CPU near-idle during the incident (threads are *blocked on I/O*, not computing); repeated restarts that line up with load; a mix of fast 503 (bulkhead) and 60s client timeouts.

**Why compute-scaling and downstream-scaling do not help:** the constraint is *request concurrency + pod lifecycle*, not CPU/memory, and requests die at the front door before reaching downstream tiers.

**Fixes (in order):**
1. Do not let liveness depend on the worker pool — relax `timeoutSeconds`/`failureThreshold`, or serve health on a dedicated connector/thread. Use **readiness** (pull from LB, let it recover) to handle overload, **not liveness** (kill).
2. Give the tier real replica headroom.
3. Make the slow endpoint truly async (accept + enqueue + return 202; heavy work off the request thread).
4. Shed load fast (bulkhead → immediate 503/429) rather than parking a thread until the client times out.

Distinguish exit-137 causes: **liveness-kill** (events say probe failed) vs **OOMKilled** (reason field says so, memory at limit). They look similar (both 137) but have opposite fixes.

Observed concretely in [[luz-docs-import upload-zip endpoint is the ingestion saturation point under perf load]] (perf k6, 2026-08-24).

## Related

- [[luz-docs-import upload-zip endpoint is the ingestion saturation point under perf load]]
