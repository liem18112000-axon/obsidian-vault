---
title: "Self-restarting kubectl port-forward keeps long-running scripts alive through drops"
created: 2026-08-13
type: howto
status: seedling
source: "luz-docs-import perf benchmark 2026-08-13"
tags: [kubectl, port-forward, kubernetes, gotcha, scripting]
---

# Self-restarting kubectl port-forward keeps long-running scripts alive through drops

A plain `kubectl port-forward` **drops** on long runs (idle resets, pod cycling, network blips) and, once dead, silently stalls whatever depends on it — e.g. a benchmark poller that then spins uselessly to its timeout. For any long-running script, wrap the forward in a **self-restarting loop** so a drop recovers in ~2 s instead of stalling the whole run:

```bash
( while true; do kubectl --context "$CTX" -n "$NS" port-forward "$TARGET" 8090:8080 >/dev/null 2>&1; sleep 2; done ) &
PF=$!
trap 'kill "$PF" 2>/dev/null' EXIT
```

Two gotchas learned the hard way:
- A `kubectl port-forward` started with `&` inside one shell invocation can die when that invocation ends; a `while`-loop daemon (or the caller's own process) keeps it alive.
- **Pod-targeted forwards are more stable than service-targeted ones.** `port-forward services/api-forwarder` (load-balanced across pods) dropped repeatedly, while `port-forward pod/<name>` to a specific pod stayed up. Prefer a specific pod for long sessions.
- Even with auto-restart, a drop *during* a timed operation still inflates that operation's client-side timing — for accuracy, read the server-side record (e.g. a job's createdAt→lastModified) rather than client wall-clock.

Related: [[Trust median and p90 over many runs, not the mean of a few, for latency on a noisy shared env]].

## Related

- [[Trust median and p90 over many runs]]
- [[not the mean of a few]]
- [[for latency on a noisy shared env]]
