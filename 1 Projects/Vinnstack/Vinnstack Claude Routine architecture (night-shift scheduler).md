---
ai_hash: fb88704aef9377e6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities:
- Claude Routine
- Vinnstack
- Vinnstack's unattended 'night shift'
- per-process scheduler
- epic
- human gate
- generation runners
- auto-snapshot
- critique
- signal
- usage-log
- Pure decision fn
- nextReadyStage
- StageAction
- DB
- LLM
- Rails (scheduler component)
- scheduler window
- AbortSignal
- run cap
- budget cap
- single-in-flight
- per-stage backoff
- freshness guard
- Postgres
- pg_try_advisory_lock
- instrumentation.ts
- Next-14 hook flag
- experimental.instrumentationHook
- Digest
- in-memory ring buffer
- GET /api/routine
- kind:'routine' signal
- Electron powerSaveBlocker
- prevent-app-suspension
- daytime idle mode
- routineEffort cap
- refresh-stories auto-run
- routine_runs retry table
- Never auto-accept AI-recommended options - they drift between runs
- Vinnstack story flows keep only the latest version - history lives in md_exports
  snapshots
source: session 2026-07-24
status: seedling
tags:
- vinnstack
- scheduler
- automation
- architecture
title: Vinnstack Claude Routine architecture (night-shift scheduler)
type: concept
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

%% ai-graph-start %%

**Related notes:**
- [[Unattended AI pipeline - auto-advance to the next human gate inside a night-shift window]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[Long agentic API routes need the inner run timeout below the route maxDuration]]
- [[Next 14 ignores instrumentation.ts without experimental.instrumentationHook]]
- [[runClaude onChunk streams stream-json; forward as NDJSON and read with getReader]]

**Relations:**
- Claude Routine — *is_part_of* — Vinnstack
- Claude Routine — *is_a* — Vinnstack's unattended 'night shift'
- Claude Routine — *is_a* — per-process scheduler
- Claude Routine — *advances* — epic
- Claude Routine — *advances_to* — human gate
- Claude Routine — *calls* — generation runners
- generation runners — *provides* — auto-snapshot
- generation runners — *provides* — critique
- generation runners — *provides* — signal
- generation runners — *provides* — usage-log
- Claude Routine — *prepares* — action
- Claude Routine — *does_not* — answer
- Claude Routine — *does_not* — approve
- Claude Routine — *does_not* — ratify
- Claude Routine — *related_to* — Never auto-accept AI-recommended options - they drift between runs
- Pure decision fn — *is_a* — nextReadyStage
- nextReadyStage — *returns* — StageAction
- Pure decision fn — *maps_state_to* — action
- Pure decision fn — *blocked_by* — human gate
- Pure decision fn — *does_not_contain* — DB
- Pure decision fn — *does_not_contain* — LLM
- Claude Routine — *resolves* — async facts
- Claude Routine — *passes* — async facts
- async facts — *to* — Pure decision fn
- Rails (scheduler component) — *lives_in* — Claude Routine
- Rails (scheduler component) — *not_in* — generation runners
- Rails (scheduler component) — *includes* — scheduler window
- scheduler window — *uses* — AbortSignal
- Rails (scheduler component) — *includes* — run cap
- Rails (scheduler component) — *includes* — budget cap
- budget cap — *uses* — usage-log
- Rails (scheduler component) — *includes* — single-in-flight
- Rails (scheduler component) — *includes* — per-stage backoff
- Rails (scheduler component) — *includes* — freshness guard
- Rails (scheduler component) — *uses* — Postgres
- Rails (scheduler component) — *uses* — pg_try_advisory_lock
- instrumentation.ts — *is_a* — Start point
- instrumentation.ts — *needs* — Next-14 hook flag
- instrumentation.ts — *related_to* — Next 14 ignores instrumentation.ts without experimental.instrumentationHook
- Digest — *is_a* — in-memory ring buffer
- in-memory ring buffer — *lives_in* — Claude Routine
- Digest — *exposed_via* — GET /api/routine
- Digest — *includes* — kind:'routine' signal
- Electron powerSaveBlocker — *is_a* — prevent-app-suspension
- Electron powerSaveBlocker — *held_while* — enabled
- daytime idle mode — *is_deferred* — feature
- routineEffort cap — *is_deferred* — feature
- generation runners — *resolve* — routineEffort
- refresh-stories auto-run — *is_deferred* — feature
- routine_runs retry table — *is_deferred* — feature
- Claude Routine — *related_to* — Vinnstack story flows keep only the latest version - history lives in md_exports snapshots

%% ai-graph-end %%