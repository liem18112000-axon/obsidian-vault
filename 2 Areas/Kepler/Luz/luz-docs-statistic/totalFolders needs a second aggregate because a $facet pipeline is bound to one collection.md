---
title: "totalFolders needs a second aggregate because a $facet pipeline is bound to one collection"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11"
tags: [luz, luz-docs-statistic, mongodb, aggregation, facet]
---

# totalFolders needs a second aggregate because a $facet pipeline is bound to one collection

When adding `totalFolders` to luz_docs_statistic (June 2026), the metric could not join the existing documents $facet: a $facet's sub-pipelines all run over the same input collection, so a per-tenant folder count requires a separate aggregate call against the `folders` collection. The job now runs two aggregates per tenant with the same cached tenant token: the documents $facet (counts + `averageFoldersPerDocument` via $avg of $size($ifNull(folderIds,[]))) and a minimal `[{$group:{_id:null,count:{$sum:1}}}]` on folders, extracted by `DocumentStatisticUtils.extractCount` (null result → 0, i.e. empty collection).

Enabler: `MongoDBService.aggregate` previously hardcoded the `documents` collection; it now has a collection-parameterized overload and the old 3-arg method delegates to it.

Semantics note: `averageFoldersPerDocument` averages over all non-being-created documents (deleted included), counting a missing `folderIds` as 0 folders.

Related: [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

## Related

- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]
