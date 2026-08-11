---
ai_hash: 15ba69a3065b10b6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-28
entities: []
source: session 2026-06-28 fulltext-ngram deck
status: seedling
tags:
- search
- mongodb
- trigram
- ngram
- indexing
- luz-docs
title: 'Trigram index makes substring search indexable: filter by 3-grams, then verify
  by regex'
type: lesson
---

# Trigram index makes substring search indexable: filter by 3-grams, then verify by regex

An unanchored, case-insensitive "contains" search (regex `^.*q.*$`, `$options:i`) can use NO B-tree index, so the database COLLSCANs every document. A trigram (n-gram) index makes substring search index-backed again, while keeping EXACT "contains" semantics.

**Mechanism — two steps:**
1. **FILTER (index, approximate):** at write time, store each document's set of overlapping 3-char shingles in an indexed multikey array (e.g. `_searchTrigrams`). A query term q (|q|>=3) decomposes to its trigrams; `{ _searchTrigrams: { $all: [...] } }` is an index seek. This rests on a **necessary condition**: if a string contains q, it contains every trigram of q. The planner leads with the rarest trigram, so keysExamined ≈ that trigram's postings, not N.
2. **VERIFY (regex, exact):** the trigram match is **not sufficient** — "strawberry" contains `tra`+`raw` but not "traw" (false positive). So run the original regex, but only over the tiny candidate set → O(few), not O(N).

Net: COLLSCAN → index seek + cheap residual verify. Tens–low-hundreds of ms at 128k docs.

**Gotchas:**
- **Write/query normalization must be the SAME function** (lowercase, diacritic-fold, whitespace) or trigrams won't line up. #1 correctness bug.
- **Index size / write amplification:** entries = Σ(unique trigrams per doc). A large OCR/body field can add thousands of trigrams per doc → bloat. Default to a **metadata-only blob**; gate body search separately.
- **Terms < 3 chars** have no trigram → can't use the index. Enforce a min-3 rule with a regex fallback for 1–2 char queries.
- Same idea as PostgreSQL `pg_trgm` / Atlas Search nGram, hand-rolled on a plain multikey index — stays inside MongoDB, no external search engine.

Related: [[Random shard key gives balanced fan-out partitions (equal-width = equal-work only if uniform)]] (same luz-docs search/perf work).

%% ai-graph-start %%

**Related notes:**
- [[luz-docs ngram search shipped code indexes the OCR body and prefilters fail-open]]
- [[ngram trigram prefilter reads the built mongo query, not the raw payload]]
- [[Bounded bucketed hashing caps trigram index entries per document]]
- [[Trigram prefilter must be field-aware only activate when every contains-regex is a _searchTrigrams field]]
- [[Larger n-grams make a substring ngram index bigger, not smaller]]

%% ai-graph-end %%