---
title: "Pipeline stages sharing a context_id need separate memory-bank path prefixes"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, test_plan_definition M1"
tags: [test-agent, memory-bank, gcs, gotcha, design-decision]
---

# Pipeline stages sharing a context_id need separate memory-bank path prefixes

In the Testing Agent, a single `context_id` (e.g. `run-6f2a`) flows through multiple stages that all read/write the **same** GCS memory bank. `knowledge_gathering`'s refine stage persists under `memory/refine/<ctx>/` (questions.json, answers.json, state.json, understanding.md). If `test_plan_definition`'s define stage — which is a refine-shaped loop on the SAME `context_id` — reused those same paths, its questions/state would silently **clobber** the refine run's.

**Fix:** give each stage its own path prefix. Define writes under `memory/test-plan/<ctx>/` (plan.json, questions.json, answers.json, decisions.json, state.json, plan-brief.md), never the refine paths. Same rule for any future stage.

**Design consequence:** `PlanSession` does NOT reuse `RefineSession` wholesale even though it is structurally a refine loop — because RefineSession's persistence is hardwired to the refine/ paths and emits `Insight`/understanding artifacts. Instead it reuses the *generic, persistence-free* helpers (`ingest`, `generate_round`, `accept_recommendation`) and owns its own state machine + `test-plan/` writers, emitting `PlanDecision`/`TestPlan`. Reuse the stateless helpers; duplicate the stateful driver when its persistence/artifacts differ.

Related technique: reuse `refine.generate_round` (a generic `(Pack, round)->questions` ranker/capper) from another stage by passing a **closure generator** that carries extra stage context (the confirmed understanding) while keeping the `(Pack, str)` signature it expects.

## Related

- [[Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton]]
