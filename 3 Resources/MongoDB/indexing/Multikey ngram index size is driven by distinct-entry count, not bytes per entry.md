---
title: "Multikey ngram index size is driven by distinct-entry count, not bytes per entry"
created: 2026-06-30
type: lesson
status: seedling
source: "luz_docs S2-index-size-options.md, 2026-06-30"
tags: [mongodb, index, trigram, ngram, luz-docs, gotcha]
---

# Multikey ngram index size is driven by distinct-entry count, not bytes per entry

A multikey index over an array field stores **one index entry per distinct array element per document**, so its size scales with **Σ(distinct elements per doc)** — the *count* of entries — not with the byte size of each key. WiredTiger prefix-compresses string keys down to ~4-5 bytes, so shrinking the key (e.g. hashing a 3-char trigram to a 2-byte int) is only a **marginal** ~20-40% lever.

The real levers cut the **number** of entries per doc: bucketing many distinct values into K buckets ([[Bounded bucketed hashing caps trigram index entries per document]]), capping the source text length, or tokenizing to words instead of char n-grams.

**Why it matters:** when an index is too big, profile *what* drives the entry count before optimizing key bytes. In luz_docs the trigram index hit 5.6 GB @512k docs purely from entry count (OCR body), and int-hashing would barely dent it.

Seen in docs/fulltext-search/S2-index-size-options.md (Kepler eArchive full-text search).

## Related

- [[Bounded bucketed hashing caps trigram index entries per document]]
- [[A large secondary index hurts via working-set vs cache]]
- [[not disk bytes]]
