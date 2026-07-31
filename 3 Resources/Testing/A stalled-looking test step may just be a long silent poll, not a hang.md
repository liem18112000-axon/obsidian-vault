---
title: "A stalled-looking test step may just be a long silent poll, not a hang"
created: 2026-07-12
type: lesson
status: seedling
source: "vinnstack BDD Run Tests scenario-timeout bugfix, 2026-07-12"
tags: [testing, timeouts, debugging, gotcha, vinnstack]
---

# A stalled-looking test step may just be a long silent poll, not a hang

A test step that produces zero stdout for many minutes isn't necessarily hung — it may be a step definition that polls/waits for an async backend job (e.g. a data-migration/backfill task) with no progress output in between, and only prints something once the wait resolves (success, failure, or its own internal poll-timeout).

If a wrapper/orchestrator around such a test enforces its OWN timeout shorter than that legitimate wait, it will kill (or think it killed) the run and report a false "timeout", even though the real test would have finished naturally — possibly with a genuinely useful result (e.g. a real assertion failure) — given enough time.

Diagnostic technique: when an orchestrated run "looks hung" from the app's point of view, check whether the underlying tool ALSO writes its own log file independent of the app's captured stdout (e.g. via `tee` in a bash script). If that file shows a full, completed result with a real timestamp/duration that the app never saw, the orchestrator's timeout fired before the real process finished — the fix is almost always to either raise the timeout (make it configurable rather than hardcoded, since different steps can have wildly different legitimate durations) or make cancellation more informative (surface partial output / elapsed time so an operator can tell "still working" from "actually stuck").

Concrete case: vinnstack's BDD "Run Tests" feature had a hardcoded 5-minute timeout for running one Cucumber/behave scenario locally. One scenario's step polls for an async backfill/migration job and took ~10.5 minutes with no stdout in between — the app reported "timeout" while the real behave process (unbeknownst to the app) kept running and eventually produced a genuine assertion failure. Fix: made the timeout configurable via `lib/core/config.ts`'s `bddVerifyScenarioTimeoutMs` (default raised to 15 minutes) instead of a bare hardcoded constant in the runner function's default parameter.

Related: [[Node child_process.kill on Windows doesn't kill descendant processes]] — the other half of this same incident: the enforced timeout didn't even successfully stop the real work, it just walked away from it. Also related in spirit to [[run_it.sh TENANT_ID is a shell parameter, not an .env value]] and the earlier `waitUntilReachable` 60s→180s fix in this same codebase — a recurring pattern this session of hardcoded timeouts being too short for real-world async operations.

## Related

- [[Node child_process.kill on Windows doesn't kill descendant processes]]
- [[run_it.sh TENANT_ID is a shell parameter]]
- [[not an .env value]]
