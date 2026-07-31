---
ai_hash: a514b6482384bac2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: vinnstack BDD Run Tests scenario-timeout bugfix, 2026-07-12
status: seedling
tags:
- testing
- timeouts
- debugging
- gotcha
- vinnstack
title: A stalled-looking test step may just be a long silent poll, not a hang
type: lesson
---

# A stalled-looking test step may just be a long silent poll, not a hang

A test step with zero stdout for many minutes is not necessarily hung: it may be polling an async backend job (migration, backfill) and print only when the wait resolves. If the orchestrator around it enforces a SHORTER timeout of its own, it reports a false "timeout" and discards a run that would have produced a real result.

**Diagnostic:** check whether the underlying tool writes its own log file independent of the app's captured stdout (e.g. `tee` in a bash wrapper). A completed result with a real duration in that file, which the app never saw, proves the orchestrator's timeout fired first.

**Fix:** make the timeout configurable rather than a hardcoded constant — legitimate step durations vary wildly — and make cancellation informative (surface partial output / elapsed time so an operator can distinguish "still working" from "stuck").

Instance: vinnstack's BDD "Run Tests" hardcoded 5 minutes per scenario; one scenario polling an async backfill took ~10.5 min and ended in a genuine assertion failure the app never saw. Fixed via `bddVerifyScenarioTimeoutMs` in `lib/core/config.ts` (default 15 min).

## Related

- [[Node child_process.kill on Windows doesn't kill descendant processes]] — same incident: the enforced timeout did not actually stop the work, it walked away from it.

%% ai-graph-start %%

**Related notes:**
- [[Node child_process.kill on Windows doesn't kill descendant processes]]
- [[Set HTTPserverless maxDuration above the internal LLM-run timeout, not below]]
- [[Long agentic API routes need the inner run timeout below the route maxDuration]]
- [[A silent canned fallback masks real failures — surface the underlying error]]
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]

%% ai-graph-end %%