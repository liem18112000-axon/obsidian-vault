---
title: "LLM query enrichment for a substring-OR matcher must contract, not expand, the token set"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 — test-agent KGA G2"
tags: [agent-design, rag, retrieval, llm, search, precision]
---

# LLM query enrichment for a substring-OR matcher must contract, not expand, the token set

If a retriever matches a query by splitting it into whitespace tokens and OR-ing per-token substring hits (match if ANY token is a substring of a node id/title/type), then **enriching the query with an LLM must CONTRACT the token set, not expand it**. More tokens can only ADD OR-clauses → strictly MORE matches, never fewer. So an LLM that "focuses semantically" by emitting exhaustive concept lists (e.g. 12 title words → 66 terms) will *broaden* recall and hurt precision, the opposite of the intent.

**Fix pattern:** constrain the LLM to a FEW (≈6) distinctive, specific terms — proper nouns, code identifiers, unique feature names — and ban broad generic words (document, system, data, service, UI, component, validation, structure, …). Add a **structural cap** (e.g. keep the first N=8 after dedup) so the token set is bounded regardless of what the model returns. Prefer precision terms that are rare in the corpus even if they match 0 today (e.g. a class name matches code-graph nodes, not Jira titles) — they add precision without inflating count.

**Corollary — selectivity is relative to the indexs domain distribution.** A term that *looks* specific can be broad in a domain-saturated index: in a 71-node index that is almost entirely eArchive-import content, the tokens `import` (21 hits) and `earchive` (26) each match ~a third of the index. Judge a terms selectivity against the actual corpus, not against how specific it reads in isolation. The real lever is which tokens you emit, and whether the matcher does substring-OR vs phrase/AND/rarity-weighted matching.

Surfaced building the test-agent KGA self-exploration (G2 hypothesize step feeding G0/G1). Related: [[Retrieval tiering: query knowledge sources cheapest and most-trusted first]], [[External LLM output is a lead generator, not a source of truth]].

## Related

- [[Retrieval tiering: query knowledge sources cheapest and most-trusted first]]
- [[External LLM output is a lead generator]]
- [[not a source of truth]]
