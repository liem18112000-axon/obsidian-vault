---
title: "Metamorphic and differential testing solve the oracle problem"
created: 2026-09-03
type: concept
status: seedling
source: "deep research 2026-09-03"
tags: [testing, oracle-problem, metamorphic, differential, api]
---

# Metamorphic and differential testing solve the oracle problem

The **oracle problem**: for many inputs there is no cheap mechanism to decide whether the observed output is correct. Two pseudo-oracles sidestep it without any ground-truth expected value:

- **Metamorphic testing** — check a **relation between the outputs of multiple runs** on transformed inputs. You never need to know either output, only that the relation holds. API examples: create-then-read must echo the written fields; adding a filter must only *narrow* a result set; re-ordering query params must not change the response; paginated pages must union to the unpaged result. A violated relation = a bug.
- **Differential testing** — run the **same input through 2+ independent implementations or versions** (old vs new deploy, two replicas, prod vs staging) and diff the responses modulo known-volatile fields. Consensus *is* the oracle; any divergence flags a regression.

**Why it matters:** both are ideal when a generator *invents* inputs and therefore has no hand-authored expected output — exactly the case for LLM/property-based test generation. Pairs naturally with property-based testing and [[Assured test generation: keep an LLM test only if it builds, passes, and raises coverage]].

## Related

- [[Assured test generation: keep an LLM test only if it builds]]
- [[passes]]
- [[and raises coverage]]
