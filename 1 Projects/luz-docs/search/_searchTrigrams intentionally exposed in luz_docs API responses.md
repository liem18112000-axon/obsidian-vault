---
title: "_searchTrigrams intentionally exposed in luz_docs API responses"
created: 2026-07-20
type: observation
status: seedling
source: "session 2026-07-20 LUZ-156314"
tags: [luz-docs, ngram, api, decision]
---

# _searchTrigrams intentionally exposed in luz_docs API responses

Decision (2026-07-20, LUZ-156314): `NgramResponseFilter` — the JAX-RS filter that stripped `_searchTrigrams` from luz_docs API responses — was deleted deliberately. The `_searchTrigrams` int-array field is now visible to API clients.

Why: stripping adds a response-rewrite cost on every read for purely cosmetic benefit; the field is just hashed trigram ints (no sensitive content recoverable), and internal `_`-prefixed fields already leak elsewhere. Not an oversight — do not "fix" by reintroducing a filter.

Related: [[Campaign-gate template: cache then campaign status L1 then repository L2]]

## Related

- [[Campaign-gate template: cache then campaign status L1 then repository L2]]
