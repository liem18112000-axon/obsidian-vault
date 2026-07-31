---
ai_hash: 16a89119c0f23249
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-27
entities: []
source: session 2026-06-27
status: seedling
tags:
- luz-docs
- search
- security
- mongodb
- ngram
title: luz-docs raw-mongo search passthrough uses an operator whitelist for security
  parity with the DSL
type: howto
---

# luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL

luz-docs `/search` originally dropped any raw-mongo `query` body (see [[luz-docs search DSL silently drops raw-mongo query keys]]). To support clients that send raw `$and`/`$or`/`$regex` while keeping the SAME security surface as the DSL, the fix translates-by-passthrough in luz-docs:

1. `SearchQuery` gains a `raw` (JsonObject) field.
2. `convertSearchQuery` (JsonObjectUtil): when no DSL key (`and`/`or`/`regexp`/`term`/`terms`/`exists`/`not`/`range`) matched and the query object is non-empty, it is raw mongo → recursively validate every `$`-prefixed key against a whitelist, else HTTP 400. Whitelist = the read-only operators the DSL can already emit: `$and $or $nor $not $regex $options $in $nin $eq $ne $gt $gte $lt $lte $exists $size $all $elemMatch $type`. Blocks `$where`/`$expr`/`$function`/`$text` etc., so raw passthrough grants no more capability than the DSL.
3. `BaseSearchService.buildJsonStoreQueries`: if `raw != null`, add it as one filter clause.

**Security parity holds because** the per-tenant security-class clause is ANDed on separately in `getQueryRequestToJsonStore` regardless of DSL-vs-raw, and the operator whitelist caps capability. Once the raw `$regex` reaches the built query, the ngram prefilter ([[ngram trigram prefilter reads the built mongo query, not the raw payload]]) fires automatically and appends `_searchTrigrams:{$all:[...]}`.

Keep the whitelist in sync if the DSL handlers gain new operators.

## Related

- [[3 Resources/Work-Kepler/luz-docs/search/luz-docs search DSL silently drops raw-mongo query keys]]
- [[3 Resources/Work-Kepler/luz-docs/search/ngram trigram prefilter reads the built mongo query, not the raw payload]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs search DSL silently drops raw-mongo query keys]]
- [[Trigram prefilter must be field-aware only activate when every contains-regex is a _searchTrigrams field]]
- [[ngram trigram prefilter reads the built mongo query, not the raw payload]]
- [[luz-docs DSL regexp value must be wrapped .term. (else HTTP 400)]]
- [[search-logic]]

%% ai-graph-end %%