---
ai_hash: d2fb1fdb5b618d49
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities:
- _searchTrigrams
- luz_docs API
- NgramResponseFilter
- JAX-RS
- API clients
- LUZ-156314
- Campaign-gate template cache then campaign status L1 then repository L2
source: session 2026-07-20 LUZ-156314
status: seedling
tags:
- luz-docs
- ngram
- api
- decision
title: _searchTrigrams intentionally exposed in luz_docs API responses
type: observation
---

# _searchTrigrams intentionally exposed in luz_docs API responses

Decision (2026-07-20, LUZ-156314): `NgramResponseFilter` — the JAX-RS filter that stripped `_searchTrigrams` from luz_docs API responses — was deleted deliberately. The `_searchTrigrams` int-array field is now visible to API clients.

Why: stripping adds a response-rewrite cost on every read for purely cosmetic benefit; the field is just hashed trigram ints (no sensitive content recoverable), and internal `_`-prefixed fields already leak elsewhere. Not an oversight — do not "fix" by reintroducing a filter.

Related: [[1 Projects/luz-docs/search/Campaign-gate template cache then campaign status L1 then repository L2]]

## Related

- [[1 Projects/luz-docs/search/Campaign-gate template cache then campaign status L1 then repository L2]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs ngram search shipped code indexes the OCR body and prefilters fail-open]]
- [[Trigram prefilter must be field-aware only activate when every contains-regex is a _searchTrigrams field]]
- [[ngram trigram prefilter reads the built mongo query, not the raw payload]]
- [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]

**Relations:**
- _searchTrigrams — *exposed in* — luz_docs API
- NgramResponseFilter — *stripped* — _searchTrigrams
- NgramResponseFilter — *is a* — JAX-RS
- NgramResponseFilter — *was deleted* — 
- _searchTrigrams — *is visible to* — API clients
- _searchTrigrams — *is an* — int-array field
- _searchTrigrams — *contains* — hashed trigram ints
- LUZ-156314 — *is related to* — _searchTrigrams
- _searchTrigrams — *is related to* — Campaign-gate template cache then campaign status L1 then repository L2

%% ai-graph-end %%