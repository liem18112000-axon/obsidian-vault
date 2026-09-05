---
title: "Retrieval tiering: query knowledge sources cheapest and most-trusted first"
created: 2026-09-03
type: concept
status: seedling
source: "session 2026-09-03 — self-exploring KGA design"
tags: [agent-design, rag, retrieval, knowledge-gathering]
---

# Retrieval tiering: query knowledge sources cheapest and most-trusted first

When an agent can consult several knowledge sources, query them in order of **increasing cost and decreasing trust**, spending an expensive/less-trusted tier only when the cheaper ones fall short. A typical ladder:

1. **Internal memory** — what the agent already gathered/verified. Free, already-trusted. Reuse beats re-fetch.
2. **In-domain authoritative search** — the org's own corpora (e.g. Jira/Confluence via JQL/CQL). Cheap, authoritative.
3. **External web** — verifiable but out-of-domain; must be **cited**.
4. **External LLM** — cheap breadth, but **unverified**; a lead generator only (see [[External LLM output is a lead generator, not a source of truth]]).

**Why it matters**
- **Bounds cost** — you do not pay for the expensive tiers on the many tickets the cheap tiers already answer.
- **Keeps results in-domain and trustworthy** — the authoritative sources dominate; the noisy tiers are last resort and gated.
- **Turns "one input" into targeted breadth** — each tier's hits become candidate seeds subject to the same scope/dedup/budget rules, so breadth never becomes unbounded drift.

This is the retrieval half of a **RAG / self-exploring agent**: hits from any tier are *promoted to seeds* and re-enter the deterministic crawl/fetch machinery unchanged. Pair with **source triangulation** (>=2 independent sources => high confidence) and a **grounding gate** on the untrusted tiers.

Surfaced designing the self-exploring Knowledge Gathering Agent (test-agent/docs/RESEARCH-self-exploring-knowledge-gather.md).

## Related

- [[External LLM output is a lead generator]]
- [[not a source of truth]]
