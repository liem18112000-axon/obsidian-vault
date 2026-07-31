---
title: "luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet aggregation"
created: 2026-06-11
type: concept
status: seedling
source: "luz_docs_statistic repo analysis, session 2026-06-11"
tags: [luz, pubsub, mongodb, aggregation, ejb-timer, luz-docs-statistic]
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

See [[luz_docs_statistic two-token model: service-tenant vs per-tenant cache token]] for which token each step uses.

## Related

- [[luz_docs_statistic two-token model: service-tenant vs per-tenant cache token]]
