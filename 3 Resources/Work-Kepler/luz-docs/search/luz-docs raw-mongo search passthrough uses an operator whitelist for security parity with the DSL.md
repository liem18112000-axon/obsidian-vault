---
title: "luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL"
created: 2026-06-27
type: howto
status: seedling
source: "session 2026-06-27"
tags: [luz-docs, search, security, mongodb, ngram]
---

# luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL

luz-docs `/search` originally dropped any raw-mongo `query` body (see [[luz-docs search DSL silently drops raw-mongo query keys]]). To support clients that send raw `$and`/`$or`/`$regex` while keeping the SAME security surface as the DSL, the fix translates-by-passthrough in luz-docs:

1. `SearchQuery` gains a `raw` (JsonObject) field.
2. `convertSearchQuery` (JsonObjectUtil): when no DSL key (`and`/`or`/`regexp`/`term`/`terms`/`exists`/`not`/`range`) matched and the query object is non-empty, it is raw mongo → recursively validate every `$`-prefixed key against a whitelist, else HTTP 400. Whitelist = the read-only operators the DSL can already emit: `$and $or $nor $not $regex $options $in $nin $eq $ne $gt $gte $lt $lte $exists $size $all $elemMatch $type`. Blocks `$where`/`$expr`/`$function`/`$text` etc., so raw passthrough grants no more capability than the DSL.
3. `BaseSearchService.buildJsonStoreQueries`: if `raw != null`, add it as one filter clause.

**Security parity holds because** the per-tenant security-class clause is ANDed on separately in `getQueryRequestToJsonStore` regardless of DSL-vs-raw, and the operator whitelist caps capability. Once the raw `$regex` reaches the built query, the ngram prefilter ([[ngram trigram prefilter reads the built mongo query, not the raw payload]]) fires automatically and appends `_searchTrigrams:{$all:[...]}`.

Keep the whitelist in sync if the DSL handlers gain new operators.

## Related

- [[luz-docs /search DSL silently drops raw-mongo query keys]]
- [[ngram trigram prefilter reads the built mongo query]]
- [[not the raw payload]]
