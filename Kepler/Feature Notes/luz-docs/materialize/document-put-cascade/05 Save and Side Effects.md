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

