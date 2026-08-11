---
ai_hash: f3fe438a959a3f11
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: luz_docs S2-index-size-options.md, 2026-06-30
status: seedling
tags:
- mongodb
- wiredtiger
- index
- performance
- cache
- luz-docs
title: A large secondary index hurts via working-set vs cache, not disk bytes
type: lesson
---

# A large secondary index hurts via working-set vs cache, not disk bytes

The real cost of a large secondary index is usually **not disk bytes** (disk is cheap) — it's whether the index's **hot working set fits the WiredTiger cache**. Once the frequently-touched index pages exceed cache, queries page postings from disk → **tail-latency spikes** and **eviction of other indexes' hot pages**, degrading unrelated queries too.

**Implication:** evaluate a big index against **available RAM / WT cache size**, not GB-on-disk. A 5.6 GB index is a problem when it crowds a cache sized for the doc working set, not because the disk is full. This is the actual reason to shrink (e.g. via [[Bounded bucketed hashing caps trigram index entries per document]]) rather than just 'accept the size'.

From luz_docs S2-index-size-options.md, Option A risk analysis (Kepler eArchive).

## Related

- [[3 Resources/Data/MongoDB/indexing/Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]

%% ai-graph-start %%

**Related notes:**
- [[Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]
- [[Larger n-grams make a substring ngram index bigger, not smaller]]
- [[OCR body text dominates a full-text trigram index]]
- [[Bounded bucketed hashing caps trigram index entries per document]]
- [[MongoDB partial index shrinks with a completing backfill]]

%% ai-graph-end %%