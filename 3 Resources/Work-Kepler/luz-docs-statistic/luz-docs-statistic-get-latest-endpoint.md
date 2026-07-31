---
ai_hash: d3deaf149c183607
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
tags:
- luz
- luz-docs-statistic
- kepler
- rest-api
- integration-test
title: luz-docs-statistic get-latest-document-statistic endpoint
---

# luz-docs-statistic — get latest document statistic

Endpoint to read a tenant's latest document statistic:

```
GET {base}/{serviceTenantId}/document-statistic/{tenantId}
```

- Example: `http://localhost:8080/luz_docs_statistic/api/8e2cb341-1c54-4ce0-96d4-27cfac64306d/document-statistic/27d1afac-02eb-4f1f-9912-fe798df22383`
- `serviceTenantId` is the `luz.docs.service.tenant.id` config (in the IT repo this is `client.id`, default `8e2cb341-1c54-4ce0-96d4-27cfac64306d`).

## Gotchas

- The handler `getLatestDocumentStatistic` only reads the **second** path param (`tenant-id`); the first `service-tenant-id` segment is **ignored** for data lookup. So passing the tenant id in both segments also "works" — an older IT helper does exactly that.
- The resource is `@PermitAll`, so **no auth token is required** (the example curl sends none). Forwarding a bearer token is fine but optional.
- Statistics are produced by a **background pub/sub + timer job**, not on write. A tenant with no record yet returns HTTP **200** with an **empty JSON object** (`{}`), because `getLatestDocumentStatisticByTenantId` returns an empty `DocumentStatisticEntity` and JSON-B omits null fields. Tests must tolerate the empty case.

## Response fields (DocumentStatisticEntity)

`_id`, `_createdDate`, `_updatedDate`, `tenantId`, `totalDocuments`, `totalDocumentsSize`,
`archivedDocuments`, `archivedDocumentsSize`, `deletedDocuments`, `deletedDocumentsSize`,
`unmaterializedDocuments`, `totalFolders`, `averageFoldersPerDocument`.

Also: `DELETE {base}/{companyTenantId}/tenant` deletes a tenant's statistic (requires `TENANT_DELETION` permission).

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]
- [[luz_docs_statistic two-token model service-tenant vs per-tenant cache token]]
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
- [[totalFolders needs a second aggregate because a $facet pipeline is bound to one collection]]
- [[luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field]]

%% ai-graph-end %%