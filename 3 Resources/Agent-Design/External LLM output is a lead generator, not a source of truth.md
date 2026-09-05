---
title: "External LLM output is a lead generator, not a source of truth"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 — self-exploring KGA design"
tags: [agent-design, rag, llm, grounding, hallucination, qa]
---

# External LLM output is a lead generator, not a source of truth

When an agent uses a **foreign public LLM** (e.g. Gemini, a different model family from the one doing the main work) as a *knowledge source*, its output must be treated as **hypotheses and search queries only** — likely subsystems, domain terms, related items, plausible edge cases — and **never written into the working set as fact**.

A **grounding gate** then admits a candidate fact only if it **resolves to a verifiable, fetchable source** (a ticket, a wiki page, a web page, actual code). Leads that cannot be corroborated are **dropped, or demoted to an explicit "unconfirmed lead"** a human can chase — they never silently enter the pack.

**Why it matters:** skipping the gate injects the LLM's hallucinations straight into whatever the pack feeds downstream. In a testing agent, an ungrounded "requirement" becomes a **wrong test**; the LLM's fluent guess is indistinguishable from a real spec until it is grounded.

**Practical corollaries**
- Use a **different model family** for the lead generator than for the downstream generator, so you do not compound one model's blind spots (same reasoning as the LLM-as-judge self-enhancement-bias caution).
- Prefer **triangulation**: a claim backed by >=2 independent sources is high-confidence; a single-source (especially LLM-only) claim is a lead, not a fact.
- The gate is the enforcement point of the tiering discipline in [[Retrieval tiering: query knowledge sources cheapest and most-trusted first]] — the external LLM is the **last, least-trusted tier**.

Surfaced designing the self-exploring Knowledge Gathering Agent (test-agent/docs/RESEARCH-self-exploring-knowledge-gather.md).

## Related

- [[Retrieval tiering: query knowledge sources cheapest and most-trusted first]]
