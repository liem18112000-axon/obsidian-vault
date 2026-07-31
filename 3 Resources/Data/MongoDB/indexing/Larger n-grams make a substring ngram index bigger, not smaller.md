---
title: "Larger n-grams make a substring ngram index bigger, not smaller"
created: 2026-06-30
type: counter-argument
status: seedling
source: "luz_docs S2-index-size-options.md, 2026-06-30"
tags: [ngram, trigram, index, gotcha, luz-docs]
---

# Larger n-grams make a substring ngram index bigger, not smaller

Reaching for **larger n-grams (4-grams instead of trigrams) to shrink** a substring index is **backwards**. Index entries = distinct n-grams per doc ([[Multikey ngram index size is driven by distinct-entry count, not bytes per entry]]). The number of *windows* in a length-L string is roughly the same for any n (L−n+1), but the **distinct n-gram space explodes**: ~37⁴ for 4-grams vs ~37³ for trigrams. Over a large body that approaches saturation, more of that larger space gets touched → **more distinct entries → bigger index**.

Larger n also raises the minimum query length (`|q|≥4` instead of ≥3), shrinking what's searchable.

So: larger n = bigger + less flexible. To shrink, go the other way (bucketing/capping/tokenizing), not bigger n.

From luz_docs S2-index-size-options.md, rejected option (Kepler eArchive).

## Related

- [[Multikey ngram index size is driven by distinct-entry count]]
- [[not bytes per entry]]
