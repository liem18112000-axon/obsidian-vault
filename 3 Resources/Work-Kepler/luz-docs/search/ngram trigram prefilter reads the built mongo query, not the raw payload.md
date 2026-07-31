---
ai_hash: e3772334f253e702
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-27
entities: []
source: session 2026-06-27
status: seedling
tags:
- luz-docs
- search
- ngram
- trigram
- mongodb
title: ngram trigram prefilter reads the built mongo query, not the raw payload
type: concept
---

# ngram trigram prefilter reads the built mongo query, not the raw payload

The ngram substring-search prefilter (`DocumentSearchService.applyTrigramPrefilter` → `collectContainsTerms`) extracts the search term by recursively scanning the **already-built** mongo query for `$regex` nodes wrapped as `^.*<term>.*$`, then unwraps to `<term>`. It does **not** read the raw incoming request payload.

**Design reason:** scanning the built query is shape-agnostic — it handles arbitrary `$and`/`$or` nesting and pulls the term regardless of how the request was phrased, as long as the regex actually made it into the built query. Single distinct term + gate-complete + min-3 chars → it ANDs `_searchTrigrams: {$all: [...]}` onto the query as the stage-1 index seek; the original `$regex` `$or` stays as the exact residual verify.

**Consequence / gotcha:** because it reads the *built* query, anything that prevents the user`s text filter from reaching the built query also disables the prefilter. The main case: a raw-`$`-mongo payload that the DSL drops (see [[luz-docs search DSL silently drops raw-mongo query keys]]) leaves no `$regex` to find → no `traw` term → no `$all` clause added. The trigram path can only fire when the regex residual is present, which is also exactly what it needs to stay correct (the `$all` is approximate; the regex removes false positives).

Part of the S2 trigram work; see [[Full-text search report]] equivalent (`project_fulltext_search_report`).

## Related

- [[3 Resources/Work-Kepler/luz-docs/search/luz-docs search DSL silently drops raw-mongo query keys]]

%% ai-graph-start %%

**Related notes:**
- [[Trigram prefilter must be field-aware only activate when every contains-regex is a _searchTrigrams field]]
- [[luz-docs ngram search shipped code indexes the OCR body and prefilters fail-open]]
- [[luz-docs search DSL silently drops raw-mongo query keys]]
- [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]]
- [[luz-docs DSL regexp value must be wrapped .term. (else HTTP 400)]]

%% ai-graph-end %%