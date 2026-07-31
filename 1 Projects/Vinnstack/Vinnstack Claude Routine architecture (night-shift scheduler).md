---
title: "Vinnstack Claude Routine architecture (night-shift scheduler)"
created: 2026-07-24
type: concept
status: seedling
source: "session 2026-07-24"
tags: [vinnstack, scheduler, automation, architecture]
---

# Vinnstack Claude Routine architecture (night-shift scheduler)

**Claude Routine** = Vinnstack's unattended 'night shift': a per-process scheduler that, inside a nightly window, advances each ENROLLED epic to its next human gate by calling the EXISTING generation runners directly — so their auto-snapshot, critique, signal, and usage-log wiring all fire unchanged. It PREPARES; humans DECIDE (it never answers, approves, or ratifies — see [[Never auto-accept AI-recommended options - they drift between runs]] for why).

Design shape worth reusing for any 'advance a state machine unattended' feature:
- **Pure decision fn** (`nextReadyStage(rec, qaFacts) → StageAction | null`): maps state → the single next action, or null when blocked on a human gate. No DB/LLM inside → unit-testable against fixtures. The scheduler resolves any async facts (QA review freshness, qa-cleared) and passes them in, keeping the fn pure.
- **Rails live in the scheduler, not the runners:** window (aborts in-flight run at window-end via AbortSignal), run cap, budget cap (usage-log spend delta since window open), single-in-flight, per-stage backoff, freshness guard (skip an epic a human edited this window), and a Postgres `pg_try_advisory_lock` so only ONE process ticks when dev + packaged both run.
- **Start point:** root instrumentation.ts (needs the Next-14 hook flag — see [[Next 14 ignores instrumentation.ts without experimental.instrumentationHook]]).
- **Digest** = in-memory ring buffer in the scheduler exposed via GET /api/routine (no cross-epic 'signals since T' query exists), plus a per-attempt `kind:'routine'` signal for the persistent audit.
- **Electron powerSaveBlocker** ('prevent-app-suspension') held while enabled so overnight ticks fire.

Deferred: daytime idle mode, routineEffort cap (runners resolve effort internally — no override param), refresh-stories auto-run, routine_runs retry table.

## Related

- [[Vinnstack story flows keep only the latest version - history lives in md_exports snapshots]]
