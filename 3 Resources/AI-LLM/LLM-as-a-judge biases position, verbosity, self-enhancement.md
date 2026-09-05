---
title: "LLM-as-a-judge biases: position, verbosity, self-enhancement"
created: 2026-09-03
type: lesson
status: seedling
source: "deep research 2026-09-03"
tags: [llm, evaluation, llm-as-judge, bias]
---

# LLM-as-a-judge biases: position, verbosity, self-enhancement

Using a strong LLM to score another model`s output (LLM-as-a-judge) is powerful but carries systematic biases you must design around:

- **Position bias** — the judge favors whichever answer is shown first. Mitigate: randomize option order (or evaluate both orders and require agreement).
- **Verbosity bias** — longer answers are scored higher regardless of quality. Mitigate: cap/normalize length; score against explicit rubric criteria.
- **Self-enhancement bias** — the judge prefers outputs from its own model family. Mitigate: use a **different model family** as judge than as generator.

**Why it matters:** these biases silently inflate scores and make an eval look trustworthy when it is not. Prefer **rubric grading** (named criteria, each scored, with chain-of-thought justification — e.g. G-Eval / DeepEval / promptfoo `llm-rubric`) over a single blob judgment, and treat the judge as a [[Assured test generation: keep an LLM test only if it builds, passes, and raises coverage|verifier]] whose own reliability must be validated.

## Related

- [[Assured test generation: keep an LLM test only if it builds]]
- [[passes]]
- [[and raises coverage]]
