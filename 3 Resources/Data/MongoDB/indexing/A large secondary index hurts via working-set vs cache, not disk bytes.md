---
title: "A large secondary index hurts via working-set vs cache, not disk bytes"
created: 2026-06-30
type: lesson
status: seedling
source: "luz_docs S2-index-size-options.md, 2026-06-30"
tags: [mongodb, wiredtiger, index, performance, cache, luz-docs]
---

# A large secondary index hurts via working-set vs cache, not disk bytes

The real cost of a large secondary index is usually **not disk bytes** (disk is cheap) — it's whether the index's **hot working set fits the WiredTiger cache**. Once the frequently-touched index pages exceed cache, queries page postings from disk → **tail-latency spikes** and **eviction of other indexes' hot pages**, degrading unrelated queries too.

**Implication:** evaluate a big index against **available RAM / WT cache size**, not GB-on-disk. A 5.6 GB index is a problem when it crowds a cache sized for the doc working set, not because the disk is full. This is the actual reason to shrink (e.g. via [[Bounded bucketed hashing caps trigram index entries per document]]) rather than just 'accept the size'.

From luz_docs S2-index-size-options.md, Option A risk analysis (Kepler eArchive).

## Related

- [[3 Resources/Data/MongoDB/indexing/Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]
