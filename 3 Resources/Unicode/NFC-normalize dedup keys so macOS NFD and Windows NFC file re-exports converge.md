---
title: "NFC-normalize dedup keys so macOS NFD and Windows NFC file re-exports converge"
created: 2026-08-11
type: lesson
status: seedling
source: "luz_docs_import LUZ-158230 · 2026-08-11"
tags: [unicode, normalization, dedup, i18n]
---

# NFC-normalize dedup keys so macOS NFD and Windows NFC file re-exports converge

When a dedup/matching **key** is derived from a file path, normalize it to **Unicode NFC + forward-slashes + no leading slash** before storing/comparing. Otherwise the *same* file re-exported from macOS (which stores names decomposed, **NFD**) and Windows (composed, **NFC**) produces byte-different keys — e.g. `hợp-đồng.pdf` fails to match itself across producers — silently defeating dedup. Especially relevant for Vietnamese/diacritic filenames.

Normalize only the **key**; keep the raw on-disk name for pairing and display (within one export, a document and its sidecar mangle consistently, so on-disk pairing already survives — only the cross-producer key drifts).

```java
Normalizer.normalize(relativePath, Normalizer.Form.NFC).replace("\\","/"); // then strip a leading "/"
```

Docker does **not** fix this: the container normalizes the runtime, not the input ZIPs bytes.

Related: [[Content-addressed dedup with a unique index and insert-first is concurrency-correct]]

## Related

- [[Content-addressed dedup with a unique index and insert-first is concurrency-correct]]
