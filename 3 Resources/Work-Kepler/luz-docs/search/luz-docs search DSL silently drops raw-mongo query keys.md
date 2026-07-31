---
title: "luz-docs /search DSL silently drops raw-mongo query keys"
created: 2026-06-27
type: lesson
status: seedling
source: "session 2026-06-27"
tags: [luz-docs, search, gotcha, mongodb]
---

# luz-docs /search DSL silently drops raw-mongo query keys

The luz-docs `/search` request DSL — parsed by `convertSearchQuery` in `JsonObjectUtil` — only recognizes **non-`$`** query keys: `and`, `or`, `regexp`, `term`, `terms`, `exists`, `not`, `range`. A raw-mongo payload using `$and` / `$or` / `$regex` is **silently dropped**, not rejected.

**Why:** the parser does `searchQueryObject.containsKey("and")` etc. `containsKey("$and") == false`, so no branch matches → `SearchQuery` ends up empty → `getQueryRequestToJsonStore` builds only the security / `_isBeingCreated` / `personal` / deletion clauses, plus a trailing empty `{}` where the user query should have been. The search then returns **all accessible docs, unfiltered** — no error.

**Symptom (logs):** the `[searchByQuery]` built query has a trailing `{}` and no `$regex`.

**Fix:** send the DSL form, no `$`:
```json
{ "query": { "or": [ {"regexp": {"title": "traw"}}, {"regexp": {"documentTitle": "traw"}} ] } }
```
The `regexp` body is `{field: term}` (a plain value); the server wraps it into `^.*term.*$` itself via `getRegexSearch`.

Historically eArchive full-text search worked (the slow COLLSCAN) because the real client sends this DSL form — a hand-crafted raw-`$`-mongo payload is what gets dropped.

See [[ngram trigram prefilter reads the built mongo query, not the raw payload]].

## Related

- [[ngram trigram prefilter reads the built mongo query]]
- [[not the raw payload]]
