---
ai_hash: 7ea8cabd0ba228c7
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
- correctness
title: 'Trigram prefilter must be field-aware: only activate when every contains-regex
  is a _searchTrigrams field'
type: lesson
---

# Trigram prefilter must be field-aware: only activate when every contains-regex is a _searchTrigrams field

The `_searchTrigrams` `$all` prefilter is a valid **necessary condition only for fields whose text was folded into the trigram blob** (`NgramCompute.STRING_FIELDS ∪ ARRAY_FIELDS`). So term extraction must be **field-aware**, not just "find any $regex".

**The bug if it is not:** a search that ORs a contains-regex over a field NOT in the blob (say `creditor` or `geoCity`) would still get `_searchTrigrams:{$all:[...]}` ANDed on. A doc that matches the term only via that non-blob field has no such trigrams in `_searchTrigrams` → the `$all` drops it → **false negative** (correct hits disappear).

**Rule implemented in `TrigramQuery.applyPrefilter`:** walk the built query; for each `field → {$regex: ^.*term.*$}`, if `field ∈ TRIGRAM_FIELDS` collect the term, else mark the whole query "unsafe". Apply the `$all` only when NOT unsafe AND exactly one distinct term. Exact-match regex (`^id$`, e.g. folderIds) is not a contains pattern → ignored, never trips unsafe. `TRIGRAM_FIELDS` is derived from `NgramCompute` so blob and gate stay in sync — to cover a new field, add it to `NgramCompute` and re-backfill.

General principle: any approximate index prefilter paired with an exact residual is only sound over the exact data the index was built from; gate activation on that scope. Relates to [[ngram trigram prefilter reads the built mongo query, not the raw payload]] and [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]].

## Related

- [[3 Resources/Work-Kepler/luz-docs/search/ngram trigram prefilter reads the built mongo query, not the raw payload]]
- [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]]

%% ai-graph-start %%

**Related notes:**
- [[ngram trigram prefilter reads the built mongo query, not the raw payload]]
- [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]]
- [[luz-docs ngram search shipped code indexes the OCR body and prefilters fail-open]]
- [[luz-docs search DSL silently drops raw-mongo query keys]]
- [[luz-docs DSL regexp value must be wrapped .term. (else HTTP 400)]]

%% ai-graph-end %%