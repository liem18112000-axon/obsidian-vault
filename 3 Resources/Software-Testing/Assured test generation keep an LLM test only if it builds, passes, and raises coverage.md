---
title: "Assured test generation: keep an LLM test only if it builds, passes, and raises coverage"
created: 2026-09-03
type: concept
status: seedling
source: "deep research 2026-09-03"
tags: [testing, llm, test-generation, coverage, agentic]
---

# Assured test generation: keep an LLM test only if it builds, passes, and raises coverage

The **assured** pattern (Meta TestGen-LLM) makes LLM-generated tests trustworthy by construction: generate a candidate, **run it**, measure a signal, and keep it **only if** it (1) compiles/binds, (2) passes reliably on repeated runs (a run-5x flakiness gate), and (3) *strictly raises coverage*. Everything else is discarded. Failures + the specific uncovered lines are fed back into the next prompt, so one blind LLM call becomes a converging generate->run->measure->gate loop.

**Why it matters:** it turns "the LLM wrote plausible tests" into "the system guarantees each emitted test builds, passes, and adds value" — no regression risk from hallucinated tests. Meta reported a funnel of ~75% built / 57% passed reliably / 25% raised coverage / 73% engineer-accepted.

**OSS to reuse:** Qodo Cover-Agent and CoverUp are open reimplementations; the four clean components are Prompt Builder, AI Caller, Test Runner, Coverage Parser. Coverage drives the loop (cheap); mutation score certifies the result (see [[Mutation score beats coverage for measuring test-suite quality]]).

## Related

- [[Mutation score beats coverage for measuring test-suite quality]]
- [[LLM-as-a-judge biases: position]]
- [[verbosity]]
- [[self-enhancement]]
