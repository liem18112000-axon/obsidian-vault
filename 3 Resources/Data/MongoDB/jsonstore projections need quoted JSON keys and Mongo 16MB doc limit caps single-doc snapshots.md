---
title: "jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots"
created: 2026-06-07
type: lesson
status: seedling
source: "materialize code review 2026-06-07"
tags: [mongodb, luz-jsonstore, luz-docs, bson, limits]
---

# jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots

Two constraints for raw jsonstore / Mongo plumbing in luz_docs:

**1. Projection strings need quoted JSON keys.** Projection/filter strings handed to luz_jsonstore are parsed via `JsonObjectUtil.createObjectByString` → strict JSON-P reader (`Json.createReader`), which rejects unquoted object keys. The canonical builder `convertQueryProjection` emits quoted keys (`"_folderNames":1`). A hand-built `"_id:1,_isPublic:1"` (unquoted) diverges from every working precedent and likely fails server-side parsing.

**2. 16MB BSON cap kills single-doc snapshot designs.** Capturing N documents into one snapshot Mongo document (array field holding all captured docs) hits the hard 16MB per-document limit at roughly 40k–200k captured docs (~150–400 bytes/doc for _id + a few sentinel fields). Unpaged `getCollectionsByFilter` reads (from/size null) also load the whole result into heap. Batch the snapshot rows or page the capture; eArchive tenants seed 128k docs, well past the cap.

## Related

- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
