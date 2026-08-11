---
ai_hash: 9a09b75b9542899b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- totalFolders
- $facet
- luz_docs_statistic
- documents collection
- folders collection
- averageFoldersPerDocument
- $avg
- $size
- $ifNull
- folderIds
- $group
- $sum
- DocumentStatisticUtils.extractCount
- MongoDBService.aggregate
- EJB timer
- PubSub
- Kepler
- Luz
- tenant token
- collection-parameterized overload
- 3-arg method
- per-tenant folder count
- job
- counts
- non-being-created documents
source: session 2026-06-11
status: seedling
tags:
- luz
- luz-docs-statistic
- mongodb
- aggregation
- facet
title: totalFolders needs a second aggregate because a $facet pipeline is bound to
  one collection
type: lesson
---

# totalFolders needs a second aggregate because a $facet pipeline is bound to one collection

When adding `totalFolders` to luz_docs_statistic (June 2026), the metric could not join the existing documents $facet: a $facet's sub-pipelines all run over the same input collection, so a per-tenant folder count requires a separate aggregate call against the `folders` collection. The job now runs two aggregates per tenant with the same cached tenant token: the documents $facet (counts + `averageFoldersPerDocument` via $avg of $size($ifNull(folderIds,[]))) and a minimal `[{$group:{_id:null,count:{$sum:1}}}]` on folders, extracted by `DocumentStatisticUtils.extractCount` (null result → 0, i.e. empty collection).

Enabler: `MongoDBService.aggregate` previously hardcoded the `documents` collection; it now has a collection-parameterized overload and the old 3-arg method delegates to it.

Semantics note: `averageFoldersPerDocument` averages over all non-being-created documents (deleted included), counting a missing `folderIds` as 0 folders.

Related: [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

## Related

- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
- [[luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field]]
- [[luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]
- [[Stale-materialized detection recomputes MaterializeCompute state via $lookup inside the statistic $facet]]

**Relations:**
- totalFolders — *needs* — second aggregate
- $facet — *pipeline is bound to* — one collection
- totalFolders — *added to* — luz_docs_statistic
- totalFolders — *could not join* — documents $facet
- $facet — *sub-pipelines run over* — same input collection
- per-tenant folder count — *requires* — separate aggregate call
- separate aggregate call — *against* — folders collection
- job — *runs* — two aggregates per tenant
- two aggregates — *use* — same cached tenant token
- documents $facet — *calculates* — counts
- documents $facet — *calculates* — averageFoldersPerDocument
- averageFoldersPerDocument — *calculated via* — $avg
- $avg — *of* — $size
- $size — *of* — $ifNull(folderIds,[])
- second aggregate — *is* — minimal $group on folders
- minimal $group on folders — *uses* — $group
- $group — *uses* — $sum
- second aggregate — *extracted by* — DocumentStatisticUtils.extractCount
- DocumentStatisticUtils.extractCount — *treats null result as* — 0
- MongoDBService.aggregate — *previously hardcoded* — documents collection
- MongoDBService.aggregate — *now has* — collection-parameterized overload
- 3-arg method — *delegates to* — collection-parameterized overload
- averageFoldersPerDocument — *averages over* — non-being-created documents
- averageFoldersPerDocument — *counts* — missing folderIds as 0 folders
- luz_docs_statistic — *updates stats via* — 1-minute EJB timer
- luz_docs_statistic — *updates stats via* — PubSub
- luz_docs_statistic — *updates stats via* — $facet aggregation
- luz_docs_statistic — *is part of* — Kepler
- luz_docs_statistic — *is part of* — Luz

%% ai-graph-end %%