---
title: "Bounded bucketed hashing caps trigram index entries per document"
created: 2026-06-30
type: technique
status: seedling
source: "luz_docs S2-index-size-options.md, 2026-06-30"
tags: [trigram, ngram, hashing, index, search, luz-docs]
---

# Bounded bucketed hashing caps trigram index entries per document

To bound the size of a substring trigram index, hash each trigram into one of **K fixed buckets** (`bucket = hash(t) mod K`) and store the set of **occupied bucket ids** instead of the trigrams themselves. A document then contributes **at most K** index entries no matter how long its text — so the per-doc entry count (and thus index size, see [[Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]) is **capped**, not linear in body length.

**Recall is preserved**: the trigram necessary condition still holds — if string S contains query q, every trigram of q is in S, so every *bucket* of q is in S's bucket set. A `$all` over the query's buckets never drops a true match.

**Cost** is more **false positives** as K shrinks: many trigrams collide into one bucket, so `$all` is coarser and the candidate set (verified by the residual regex) grows. **K is the size↔selectivity dial** — small K = tiny index but heavier residual verify (toward COLLSCAN-sized). Benchmark candidate-set size at K∈{256,512,1024,2048} on real data before committing.

Write-path and query-path must use the **identical** bucket function or recall silently breaks.

From luz_docs S2-index-size-options.md, Option C (Kepler eArchive).

## Related

- [[Multikey ngram index size is driven by distinct-entry count]]
- [[not bytes per entry]]
- [[OCR body text dominates a full-text trigram index]]
