---
title: "luz-docs DSL regexp value must be wrapped .*term.* (else HTTP 400)"
created: 2026-06-27
type: lesson
status: seedling
source: "session 2026-06-27"
tags: [luz-docs, search, gotcha, validation]
---

# luz-docs DSL regexp value must be wrapped .*term.* (else HTTP 400)

In a luz-docs `/search` DSL `regexp` clause, the value string **must already be wrapped** as `.*<term>.*` — it has to start with `.*` AND end with `.*`, and be longer than 4 chars. `SearchSingleOperatorServiceValidation.validateHandleRegexpQuery` rejects anything else with HTTP 400 `query element of 'regexp' is invalid` (`query.element.is.invalid`).

So the correct contains-search body is:
```json
{ "query": { "or": [ { "regexp": { "title": ".*traw.*" } } ] } }
```
NOT `{"regexp":{"title":"traw"}}` (that 400s — too short + missing the `.*` markers).

Server-side `getRegexSearch` then strips the leading/trailing `.*` and re-wraps the literal into `^.*traw.*$` with `$options:"i"` before it reaches mongo. So the `.*` you send is a required DSL marker, not part of the matched text.

Pairs with [[luz-docs search DSL silently drops raw-mongo query keys]] (raw `$regex`/`$and` is dropped entirely; this is the correct DSL alternative) and the ngram prefilter [[ngram trigram prefilter reads the built mongo query, not the raw payload]].

## Related

- [[3 Resources/Work-Kepler/luz-docs/search/luz-docs search DSL silently drops raw-mongo query keys]]
- [[3 Resources/Work-Kepler/luz-docs/search/ngram trigram prefilter reads the built mongo query, not the raw payload]]
