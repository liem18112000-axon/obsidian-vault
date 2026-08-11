---
ai_hash: af938005b0e3c646
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: luz_docs S2-index-size-options.md, 2026-06-30
status: seedling
tags:
- trigram
- ngram
- hashing
- index
- search
- luz-docs
title: Bounded bucketed hashing caps trigram index entries per document
type: technique
---

# Bounded bucketed hashing caps trigram index entries per document

To bound the size of a substring trigram index, hash each trigram into one of **K fixed buckets** (`bucket = hash(t) mod K`) and store the set of **occupied bucket ids** instead of the trigrams themselves. A document then contributes **at most K** index entries no matter how long its text — so the per-doc entry count (and thus index size, see [[Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]) is **capped**, not linear in body length.

**Recall is preserved**: the trigram necessary condition still holds — if string S contains query q, every trigram of q is in S, so every *bucket* of q is in S's bucket set. A `$all` over the query's buckets never drops a true match.

**Cost** is more **false positives** as K shrinks: many trigrams collide into one bucket, so `$all` is coarser and the candidate set (verified by the residual regex) grows. **K is the size↔selectivity dial** — small K = tiny index but heavier residual verify (toward COLLSCAN-sized). Benchmark candidate-set size at K∈{256,512,1024,2048} on real data before committing.

Write-path and query-path must use the **identical** bucket function or recall silently breaks.

From luz_docs S2-index-size-options.md, Option C (Kepler eArchive).

## Related

- [[3 Resources/Data/MongoDB/indexing/Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]
- [[OCR body text dominates a full-text trigram index]]

%% ai-graph-start %%

**Related notes:**
- [[OCR body text dominates a full-text trigram index]]
- [[Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]
- [[Larger n-grams make a substring ngram index bigger, not smaller]]
- [[Trigram index makes substring search indexable filter by 3-grams, then verify by regex]]
- [[A large secondary index hurts via working-set vs cache, not disk bytes]]

%% ai-graph-end %%