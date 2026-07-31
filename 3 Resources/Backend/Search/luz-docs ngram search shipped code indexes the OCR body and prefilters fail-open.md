---
ai_hash: e4c9dc324e9fb057
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-28
entities: []
source: session 2026-06-28 ngram code review
status: seedling
tags:
- search
- mongodb
- trigram
- ngram
- luz-docs
- implementation
title: 'luz-docs ngram search: shipped code indexes the OCR body and prefilters fail-open'
type: reference
---

# luz-docs ngram search: shipped code indexes the OCR body and prefilters fail-open

Two facts about the **shipped** `ch.klara.luz.docs.ngram` package that diverge from / sharpen the S2 design doc:

**1. The trigram blob INCLUDES `documentTextContent` (the full OCR body).** `NgramCompute.STRING_FIELDS` = title, documentTitle, documentDescription, senderName, subject, senderEndToEndId, fileName, **documentTextContent**; ARRAY_FIELDS = tags, documentTypes. The design doc had recommended *metadata-only* to avoid index bloat — the code chose to index the body too, so search reaches inside document text, accepting a larger `idx_trigrams`. (Levers if it hurts: cap body length / separate body-trigram field.)

**2. Query integration is a fail-open PREFILTER, not a rewrite.** `NgramFacade.applyTrigramPrefilter` → if `NgramGate.isTrigramComplete` (cached 3600s/60s) is false, return the query unchanged (plain regex). Else `TrigramQuery.applyPrefilter` walks the query tree for unanchored contains-regexes (`^.*term.*$`) on the searchable fields, and:
- if a contains-term targets a field NOT covered by the trigram blob, OR there is more than one distinct term → **bail, keep the plain regex** (ANDing `$all` would wrongly drop docs matching only via the uncovered field / other term).
- otherwise AND `{ _searchTrigrams: { $all: trigrams } }` onto the query and **leave the original regex in place** — so the planner does IXSCAN idx_trigrams + regex residual verify. Correctness over speed.

Other code facts: `TrigramGenerator.normalize` = lowercase(ROOT) → NFD + strip combining marks → collapse whitespace → trim; write-side and query-side share it (the #1 correctness rule). `NgramMigrationExecutor` backfills BATCH_SIZE=300 under Semaphore(3), COMPLETED only if zero docs fail. Gate's untrigrammed count uses `_searchTrigrams $exists:false` (a COLLSCAN, acceptable because cached and returns 0 once done).

Related: [[3 Resources/Backend/Search/Trigram index makes substring search indexable filter by 3-grams, then verify by regex]].

%% ai-graph-start %%

**Related notes:**
- [[ngram trigram prefilter reads the built mongo query, not the raw payload]]
- [[Trigram prefilter must be field-aware only activate when every contains-regex is a _searchTrigrams field]]
- [[Trigram index makes substring search indexable filter by 3-grams, then verify by regex]]
- [[OCR body text dominates a full-text trigram index]]
- [[_searchTrigrams intentionally exposed in luz_docs API responses]]

%% ai-graph-end %%