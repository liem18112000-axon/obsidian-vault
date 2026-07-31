---
ai_hash: 111d5ae391b0a283
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz_docs_statistic
- EJB timer
- Pub/Sub
- $facet aggregation
- 1-minute EJB timer
- '@Schedule timer'
- UpdateDocumentStatisticTimer
- UPDATE_DOCUMENT_STATISTIC_MAX_RECORD
- Google Pub/Sub
- luz.docs.document.statistic.sub
- tenantId
- documents collection
- jsonstore
- archived facet
- deleted facet
- total facet
- count
- summed size
- isBeingCreated
- documentstatistics
- service tenant
- totalDocuments
- Byte suffix
- two-token model
- service-tenant cache token
- per-tenant cache token
- luz_docs_statistic two-token model service-tenant vs per-tenant cache token
source: luz_docs_statistic repo analysis, session 2026-06-11
status: seedling
tags:
- luz
- pubsub
- mongodb
- aggregation
- ejb-timer
- luz-docs-statistic
title: luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet
  aggregation
type: concept
---

# luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet aggregation

luz_docs_statistic recomputes per-tenant document statistics on a pull-based cycle rather than reacting per event:

1. An EJB `@Schedule` timer fires every minute (`UpdateDocumentStatisticTimer`, non-persistent).
2. It synchronously pulls + acks up to `UPDATE_DOCUMENT_STATISTIC_MAX_RECORD` (default 100) Google Pub/Sub messages from subscription `luz.docs.document.statistic.sub`; each message just names a `tenantId` whose documents changed.
3. Tenant IDs are de-duplicated, then per tenant a single $facet aggregation runs over the `documents` collection (via jsonstore) computing three facets — archived, deleted, total — each as count + summed size. Docs with `isBeingCreated=true` are $match-excluded first.
4. The result is upserted into `documentstatistics` under the service tenant; an insert only happens when `totalDocuments != 0`.

Quirks worth remembering:
- Document sizes are stored as **strings with a `Byte` suffix** (e.g. "3028Byte"), not numbers — anything consuming the entity must parse that.
- Pub/Sub messages are only triggers; the aggregation always recomputes from scratch, so duplicates/lost increments are harmless (eventually-consistent by design).

See [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic two-token model service-tenant vs per-tenant cache token]] for which token each step uses.

## Related

- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]
- [[totalFolders needs a second aggregate because a $facet pipeline is bound to one collection]]
- [[luz-docs-statistic-get-latest-endpoint]]
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
- [[luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field]]

**Relations:**
- luz_docs_statistic — *updates stats via* — 1-minute EJB timer
- luz_docs_statistic — *updates stats via* — Pub/Sub
- luz_docs_statistic — *updates stats via* — $facet aggregation
- 1-minute EJB timer — *is* — EJB timer
- EJB timer — *uses* — @Schedule timer
- @Schedule timer — *is named* — UpdateDocumentStatisticTimer
- UpdateDocumentStatisticTimer — *pulls messages from* — Google Pub/Sub
- Google Pub/Sub — *has subscription* — luz.docs.document.statistic.sub
- luz.docs.document.statistic.sub — *provides* — tenantId
- UpdateDocumentStatisticTimer — *pulls up to* — UPDATE_DOCUMENT_STATISTIC_MAX_RECORD
- $facet aggregation — *runs over* — documents collection
- documents collection — *accessed via* — jsonstore
- $facet aggregation — *computes* — archived facet
- $facet aggregation — *computes* — deleted facet
- $facet aggregation — *computes* — total facet
- archived facet — *includes* — count
- archived facet — *includes* — summed size
- deleted facet — *includes* — count
- deleted facet — *includes* — summed size
- total facet — *includes* — count
- total facet — *includes* — summed size
- documents collection — *excludes documents with* — isBeingCreated
- result — *is upserted into* — documentstatistics
- documentstatistics — *under* — service tenant
- insert into documentstatistics — *requires* — totalDocuments != 0
- Document sizes — *stored as* — strings with a Byte suffix
- Pub/Sub messages — *are* — triggers
- luz_docs_statistic — *uses* — two-token model
- two-token model — *involves* — service-tenant cache token
- two-token model — *involves* — per-tenant cache token
- luz_docs_statistic — *described in* — luz_docs_statistic two-token model service-tenant vs per-tenant cache token

%% ai-graph-end %%