---
title: "OCR body text dominates a full-text trigram index"
created: 2026-06-30
type: observation
status: seedling
source: "luz_docs S2 benchmark, 2026-06-30"
tags: [trigram, ngram, fulltext, index, luz-docs]
---

# OCR body text dominates a full-text trigram index

When a substring trigram field concatenates both metadata (title, fileName, sender, subject, tags) and a **full OCR body** (`documentTextContent`), the **body dominates** the index. Metadata fields yield only tens-to-low-hundreds of distinct trigrams per doc; a multi-paragraph OCR body saturates **thousands**.

Measured in luz_docs @512k docs: `idx_trigrams` = 5.6 GB = **~98% of all index storage**, ~linear at ~11 MB / 1 000 docs, driven almost entirely by the body.

**Design consequence:** treat the body as the expensive, optional part. Either drop it from the trigram blob (metadata-only), isolate it in a separate field+index so the hot metadata path stays tiny and body search is flag-gated, or bound it via [[Bounded bucketed hashing caps trigram index entries per document]] / length-cap. Don't let body trigrams ride in the same always-on field as metadata.

From luz_docs S2-index-size-options.md + S2-benchmark-perf-scaling.md (Kepler eArchive).

## Related

- [[3 Resources/Data/MongoDB/indexing/Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]
- [[Bounded bucketed hashing caps trigram index entries per document]]
