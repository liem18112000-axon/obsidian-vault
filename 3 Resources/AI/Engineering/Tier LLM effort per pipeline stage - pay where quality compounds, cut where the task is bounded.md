---
ai_hash: 5c0e461dfa55bf89
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22
status: seedling
tags:
- llm
- effort
- latency
- cost
- pipeline
- vinnstack
title: Tier LLM effort per pipeline stage - pay where quality compounds, cut where
  the task is bounded
type: model
---

# Tier LLM effort per pipeline stage - pay where quality compounds, cut where the task is bounded

In a multi-stage LLM pipeline, one global effort/reasoning knob wastes money and latency: the cheap frequent stages (a 1-5 critic rubric that runs after EVERY generation) burn the same effort as the hardest stage (code-grounded architecture synthesis). Tier effort per stage instead:

- **Pay MORE (xhigh)** where output quality compounds downstream: code-exploring design rounds, writing real test code.
- **Default (high)** for judgment stages whose inputs are already settled: PRD synthesis, slicing/classification, review gates (recall degrades below high).
- **Cut (medium/low)** for bounded tasks: fixed-rubric critics, structured Gherkin from settled answers, checklist scans, aggregations. The frequent low-stakes critic at `low` is usually the biggest cumulative saving.

Implementation shape (Vinnstack, lib/core/config.ts): `DEFAULT_EFFORT_BY_KIND` map in code + optional operator `effortByKind` override in config, resolved `operator map > tuned default > family-wide knob` at the runner's overrides builder. Effort stays a **runtime/config knob — never in SKILL.md**: skills are portable procedures (Polaris contributions); effort is model- and budget-specific, so embedding it would go stale per-model and impose one team's cost policy on every consumer.

Related: [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]

%% ai-graph-start %%

**Related notes:**
- [[Route tool-less LLM passes to a cheaper model tier]]
- [[Unattended AI pipeline - auto-advance to the next human gate inside a night-shift window]]
- [[Find then adversarial-refute verify pass cuts AI reviewer false positives]]
- [[AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run]]
- [[Keep the release gate deterministic; put AI judgment in upstream signals not the gate]]

%% ai-graph-end %%