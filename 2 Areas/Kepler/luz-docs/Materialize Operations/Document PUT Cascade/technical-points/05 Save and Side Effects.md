---
ai_hash: 0720f8215ba696db
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- 05 Save and Side Effects
- document-put-cascade
- 04 Materialized Fields Computation
- 06 Failure Paths
- Materialized fields
- User metadata
- JsonObjectUtil.mergeTechnicalFields
- metadata
- computedFields
- jsonStoreMongoService.updateDocumentMetadata
- document
- token
- tenantId
- documentId
- versionNumber
- diagrams/05-save-side-effects.png
- Target user metadata
- Computed source object
- Server-generated technical fields
- Internal fields
- Normal user fields
- Request body
- Side effects
- Document statistics event
- validatePutUpdateForDocumentStatistic
- currentMetadata
- Audit success log
- Document update
- Background retry marker
- Materialization
- Merge (concept)
- Side effect (concept)
- Audit log (concept)
- Statistic event (concept)
- Synchronous (concept)
- API request
- caller
- Document update success
---

# Save and Side Effects

[[document-put-cascade|Back to index]] | Previous: [[04 Materialized Fields Computation|Materialized fields computation]] | Next: [[06 Failure Paths|failure paths]]

After materialized fields are computed, they are merged into the user metadata:

```java
metadata = JsonObjectUtil.mergeTechnicalFields(metadata, computedFields);
```

Then the final document is saved:

```java
jsonStoreMongoService.updateDocumentMetadata(token, tenantId, documentId, metadata, versionNumber);
```

## Visual explanation

![[diagrams/05-save-side-effects.png]]

## What `mergeTechnicalFields` does

`mergeTechnicalFields` copies:

1. All fields from the target user metadata.
2. Only fields starting with `_` from the computed source object.

This means the server-generated technical fields overwrite or add internal fields, while normal user fields come from the request body.

## Side effects after save

After saving, the service may do extra work:

| Side effect | When / why |
|---|---|
| Document statistics event | If `validatePutUpdateForDocumentStatistic(currentMetadata, metadata)` says statistics might change. |
| Audit success log | Records that the document update succeeded. |

## Important behavior

The materialized fields are saved in the same document update as the user's metadata. There is no background retry marker for this path.

If materialization fails, the document update fails instead of saving a partially refreshed document.

## Technical terms

| Term | Newbie explanation |
|---|---|
| Merge | Combine two objects into one. |
| Side effect | Extra work caused by the main operation, such as logging or cache/statistic updates. |
| Audit log | A record of who did what, useful for compliance and debugging. |
| Statistic event | A message telling another part of the app to refresh counts or cached statistics. |
| Synchronous | Work happens before the API request finishes. The caller waits for it. |

%% ai-graph-start %%

**Related notes:**
- [[02 Service Validation]]
- [[06 Failure Paths]]
- [[01 API Entry Point]]
- [[03 Cascade Decision Gate]]
- [[04 Materialized Fields Computation]]

**Relations:**
- 05 Save and Side Effects — *is part of* — document-put-cascade
- 05 Save and Side Effects — *follows* — 04 Materialized Fields Computation
- 05 Save and Side Effects — *precedes* — 06 Failure Paths
- Materialized fields — *are merged into* — User metadata
- JsonObjectUtil.mergeTechnicalFields — *merges* — computedFields
- JsonObjectUtil.mergeTechnicalFields — *into* — metadata
- metadata — *is updated by* — JsonObjectUtil.mergeTechnicalFields
- document — *is saved by* — jsonStoreMongoService.updateDocumentMetadata
- jsonStoreMongoService.updateDocumentMetadata — *takes parameter* — token
- jsonStoreMongoService.updateDocumentMetadata — *takes parameter* — tenantId
- jsonStoreMongoService.updateDocumentMetadata — *takes parameter* — documentId
- jsonStoreMongoService.updateDocumentMetadata — *takes parameter* — metadata
- jsonStoreMongoService.updateDocumentMetadata — *takes parameter* — versionNumber
- 05 Save and Side Effects — *has visual explanation* — diagrams/05-save-side-effects.png
- JsonObjectUtil.mergeTechnicalFields — *copies fields from* — Target user metadata
- JsonObjectUtil.mergeTechnicalFields — *copies fields from* — Computed source object
- Server-generated technical fields — *overwrite or add* — Internal fields
- Normal user fields — *originate from* — Request body
- Side effects — *occur after* — Document update
- Document statistics event — *is a type of* — Side effects
- Document statistics event — *is triggered by* — validatePutUpdateForDocumentStatistic
- validatePutUpdateForDocumentStatistic — *takes parameter* — currentMetadata
- validatePutUpdateForDocumentStatistic — *takes parameter* — metadata
- Audit success log — *is a type of* — Side effects
- Audit success log — *records* — Document update success
- Materialized fields — *are saved in* — Document update
- Document update — *lacks* — Background retry marker
- Document update — *fails if* — Materialization fails
- Merge (concept) — *is defined as* — Combine two objects into one
- Side effect (concept) — *is defined as* — Extra work caused by the main operation
- Audit log (concept) — *is defined as* — A record of who did what
- Statistic event (concept) — *is defined as* — A message telling another part of the app to refresh counts or cached statistics
- Synchronous (concept) — *is defined as* — Work happens before the API request finishes
- Synchronous (concept) — *implies* — caller waits
- API request — *completes after* — Synchronous (concept)

%% ai-graph-end %%