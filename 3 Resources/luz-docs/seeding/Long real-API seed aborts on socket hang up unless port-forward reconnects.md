---
title: "Long real-API seed aborts on socket hang up unless port-forward reconnects"
created: 2026-06-22
type: lesson
status: seedling
source: "session 2026-06-22"
tags: [luz-docs, kubectl, port-forward, resilience, gotcha]
---

# Long real-API seed aborts on socket hang up unless port-forward reconnects

A document-seeding loop that drives a REST endpoint through a kubectl port-forward must treat a transport error (socket hang up / ECONNRESET / timeout) as **retryable**, not fatal — and the retry must first **re-establish the port-forward**, because a single dropped pf makes every subsequent request fail.

In the `earchive-data-prepare` skill the per-doc `postOne` only caught HTTP 5xx (backoff+retry); a raw transport rejection propagated straight to the worker and set the shared `aborted`, killing the whole run. A 30k-doc run died at 12443/30000 after ~25 min when the port-forward dropped. Worse, the run had **reused an externally-owned port-forward** ("already reachable — reusing", so the skill held `realApiPf=null`) — so it could not even kill/respawn what it did not own.

Fix shape:
- Wrap the request in try/catch inside the retry loop.
- On transport error: call a **serialized** `reconnectRealApiPf()` (one reconnect even when N concurrent workers all hit the drop) that no-ops if the port is reachable again, else kills the old pf and re-runs `ensureApiPortForward()` (which spawns a fresh pf when the port is closed).
- Back off with the existing escalating schedule and cap at `NET_MAX_RETRIES=5` per doc before giving up.

General lesson: any long-lived tunnel (port-forward, SSH, VPN) WILL drop on a multi-hour job; resilience means reconnect-then-retry, not just retry. Reusing a tunnel you do not own is a trap — you cannot recover it.

Related: [[Resume a partial earchive seed with APPEND instead of re-truncating]]

## Related

- [[Resume a partial earchive seed with APPEND instead of re-truncating]]
