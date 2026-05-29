# Failure Paths

[[document-put-cascade|Back to index]] | Previous: [[05 Save and Side Effects|save and side effects]] | Next: [[07 Files of Record|files of record]]

This `PUT` path has several ways to fail before saving.

## Visual explanation

![[diagrams/06-failure-paths.png]]

## Common failure points

| Failure point | What happens |
|---|---|
| Invalid document ID | Request fails before loading the document. |
| Body ID does not match path ID | Request fails to prevent updating the wrong document. |
| Version mismatch | Request fails to prevent stale overwrite. |
| Deletion status changed | Request fails because deletion state cannot be changed through this endpoint. |
| Bad `folderIds` format | Logs a warning and throws `DocumentException` with bad request status. |
| Folder ID does not exist | Request fails before save. |
| Client changed technical fields | Request fails before materialized fields are recomputed. |
| Materialize repository problem | `MaterializeCascadeException` can trigger retry behavior in `onDocumentChange`. |
| Materialize retries exhausted | Fallback throws `DocumentException(CAN_NOT_UPDATE_METADATA_IN_JSONSTORE, 500)`. |

## Retry behavior inside computation

`MaterializeCascadeService.onDocumentChange` has MicroProfile Fault Tolerance annotations:

```java
@Retry(
    delay = 500,
    retryOn = MaterializeCascadeException.class,
    abortOn = { NullPointerException.class, IllegalArgumentException.class }
)
@Fallback(fallbackMethod = "onCascadeFailed")
```

In plain English:

1. If folder loading or materialized computation fails with `MaterializeCascadeException`, retry after 500 ms.
2. Do not retry obvious programming/input problems like `NullPointerException` or `IllegalArgumentException`.
3. If retries are exhausted, log an error and fail the document update with HTTP 500.

## Technical terms

| Term | Newbie explanation |
|---|---|
| Failure path | What the code does when something goes wrong. |
| Bad request | HTTP 400; the caller sent invalid data. |
| HTTP 500 | Server-side failure. |
| Retry | Try the same operation again after a short delay. |
| Fallback | Backup method called when retries do not solve the problem. |
| Exception | Java object representing an error. |
| Stale overwrite | Accidentally replacing newer data with older data. |

