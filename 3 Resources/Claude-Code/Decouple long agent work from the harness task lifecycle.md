---
title: "Decouple long agent work from the harness task lifecycle"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 (perf-cluster 2.2M seed)"
tags: [claude-code, background-tasks, orchestration, ops, resilience]
---

# Decouple long agent work from the harness task lifecycle

A long-running job launched as an agent harness "background task" is tied to that tasks lifecycle: the harness can **stop the tracked task** after some wall-clock window (observed ~1h45m in Claude Code), and you then stop receiving its completion notification. Observed nuance: the **OS processes it spawned keep running as reparented orphans** even after the harness "kills" the task — so the work itself survives, but youve lost your signal for when it finishes.

**Fix — decouple work from notification:**
- Run the long **worker DETACHED** (`nohup … & disown`) so it is independent of the harness task lifecycle and never gets the timed kill.
- Run a **separate lightweight tracked background task as the MONITOR** that polls the end-state and exits 0 at target (→ re-invokes the agent to notify the user) or exits non-zero if the worker died (→ relaunch the worker).
- If the harness kills the *monitor*, relaunch only the monitor; the detached worker is untouched. If it kills the *worker* (rare, since detached), the monitor detects the stall and asks for a relaunch.

The monitor should measure the **authoritative end-state directly** (e.g. a row/doc count) rather than trusting the workers own logs or exit code — see the exit-0-can-lie corollary in [[kubectl port-forward drops after ~1 hour on GKE]]. Pairs with [[Resume a large append seed by recounting to a target]], which makes each worker relaunch loss-free. Detaching on Git Bash: [[Git Bash on Windows has no setsid; detach with nohup and disown]].

## Related

- [[Resume a large append seed by recounting to a target]]
- [[kubectl port-forward drops after ~1 hour on GKE]]
- [[Git Bash on Windows has no setsid; detach with nohup and disown]]
