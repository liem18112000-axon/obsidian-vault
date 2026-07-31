---
title: "Route tool-less LLM passes to a cheaper model tier"
created: 2026-07-09
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [llm, cost-optimization, architecture]
---

# Route tool-less LLM passes to a cheaper model tier

When an LLM pipeline has multiple distinct sub-tasks, route the ones that are pure classification, rating, or JSON-extraction (no tool use, no multi-step reasoning) to a cheaper/faster model tier, and reserve the expensive frontier model for the sub-tasks that actually need its reasoning depth (long-document synthesis, code grounding, multi-turn tool use).

## Why

Cost scales with which model answers, not just how many tokens are sent. A 1-5 star rating with a one-paragraph rationale, or "decompose this document into a JSON list," doesn't benefit from a frontier model's extra reasoning — but if every call in a pipeline defaults to the same model as the "real" generation step, these small transforms silently pay full frontier-model rates for work a cheaper tier handles identically.

## Concrete case

In vinnstack, `lib/ultracode/ultracodeRunner.ts` defines two model constants: `CLAUDE_MODEL` (defaults to Opus, used for real generation) and `CHEAP_MODEL` (defaults to Sonnet, explicitly documented as "Cheaper model for tool-less background transforms... Opus is wasted on these pure JSON passes; this is the main lever on the framework's token cost"). It's correctly wired into the chat app's background passes (conversation summarization, skill extraction, follow-up extraction) — but a parallel subsystem (the Interrogation Room's AI self-assessment/critique pass, and two other tool-less JSON-decomposition passes) never adopted it, defaulting to the expensive model for calls that are structurally identical to the ones `CHEAP_MODEL` was built for. The fix pattern: add an optional model-override parameter to the shared LLM-call wrapper, and pass the cheap constant at the specific call sites that are tool-less/classification-shaped.

## How to spot this in a review

Grep for every distinct LLM call site in a codebase and check tool count per call: `tools: []` or an empty/near-empty allowlist combined with "output only JSON, no prose" phrasing in the system prompt is the signature of a call that’s a cheap-model candidate, regardless of which model the surrounding pipeline defaults to.

## Related
- [[Unrestricted directory mounts for LLM tools risk multi-megabyte single-tool-call reads]]
