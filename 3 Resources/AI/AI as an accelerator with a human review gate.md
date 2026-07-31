---
title: "AI as an accelerator with a human review gate"
created: 2026-07-20
type: argument
status: seedling
source: "interview prep, session 2026-07-20"
tags: [ai, testing, qa, sdet, review-gate, career]
---

# AI as an accelerator with a human review gate

Treat AI as an **accelerator behind a human review gate**, never as the final verdict. It compresses the time to a *candidate* answer; a human plus a known-good baseline decide whether the candidate is actually correct.

## Where it applies
- **Test generation** — derive edge cases from diffs, but human-review before merge.
- **Code-review assistance** — surface issues, don't auto-approve.
- **Defect triage / clustering** — let an LLM cluster failures by pattern; a human confirms root cause.

## The trust boundary
Validate every AI output against a **golden set / known-good baseline** before trusting it in a pipeline. Withhold trust entirely for:
- security / compliance assertions, and
- anything with **no golden-set validation step**.

Also flag, never silently trust, self-healing test locators — an auto-healed match can bind to the wrong element.

## Why it holds
The failure mode isn't that AI is wrong often — it's that it's *confidently wrong in ways that pass*: a generated test that goes green while asserting the wrong condition, a locator that matches the wrong node. Only a baseline comparison, not the model's own confidence, catches those.

Context: my own Java / Jakarta REST / MongoDB work; used as the core narrative for SDET/QA interviews.

## Related

- [[NVIDIA Vietnam SDET Interview Prep]]
