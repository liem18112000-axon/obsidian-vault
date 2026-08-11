---
ai_hash: 070eba1f66b477c3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22
status: seedling
tags:
- scheduling
- automation
- llm
- pipeline
- vinnstack
title: Unattended AI pipeline - auto-advance to the next human gate inside a night-shift
  window
type: model
---

# Unattended AI pipeline - auto-advance to the next human gate inside a night-shift window

Design for scheduled/unattended LLM generation (Vinnstack "Claude Routine", plan at doc/claude-routine-implementation-plan.md, 2026-07-22):

- **Auto-advance, not schedules-per-step**: the pipeline's state machine already encodes readiness, so the scheduler's only question per enrolled item is "what is the next generation this item is waiting for?" — advance one stage, stop at every human gate (answers, approvals, ratification). No per-step config to maintain.
- **The routine prepares; humans decide** — hard rule, because AI-recommended options drift between runs (sampling): unattended acceptance would embed noise as decisions. The unattended win is a reviewable *morning docket*, not autonomy.
- **Night shift beats literal 24/7** when the LLM runs on a subscription quota (5h windows / weekly caps): a 22:00–06:00 window spends quota the operator would waste asleep without starving daytime interactive use. Rails: run cap + budget cap (from the existing per-run usage log), sequential single run, backoff, and a freshness guard (never regenerate what a human touched since the last routine pass).
- Cheap infra: in-process tick loop started from Next instrumentation.ts (no OS cron), direct server-side runner calls (not HTTP self-calls) so existing snapshot/critique/signal/usage wiring fires unchanged, pg_advisory_lock so two app instances don't both tick, Electron powerSaveBlocker for the window.

Related: [[Tier LLM effort per pipeline stage - pay where quality compounds, cut where the task is bounded]], [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack Claude Routine architecture (night-shift scheduler)]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[Long agentic API routes need the inner run timeout below the route maxDuration]]
- [[vinnstack spawns the local claude CLI for subscription-authenticated automation]]
- [[Tier LLM effort per pipeline stage - pay where quality compounds, cut where the task is bounded]]

%% ai-graph-end %%