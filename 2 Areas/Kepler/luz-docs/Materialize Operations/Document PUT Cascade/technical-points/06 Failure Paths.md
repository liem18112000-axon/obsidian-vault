---
ai_hash: ac5eb48c14ec84ff
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Failure Paths
- document-put-cascade
- 05 Save and Side Effects
- 07 Files of Record
- PUT path
- diagrams/06-failure-paths.png
- Invalid document ID
- Body ID does not match path ID
- Version mismatch
- Deletion status changed
- Bad `folderIds` format
- '`DocumentException`'
- Bad Request Status
- Folder ID does not exist
- Client changed technical fields
- Materialize repository problem
- '`MaterializeCascadeException`'
- '`onDocumentChange`'
- Materialize retries exhausted
- '`CAN_NOT_UPDATE_METADATA_IN_JSONSTORE`'
- HTTP 500
- '`MaterializeCascadeService.onDocumentChange`'
- MicroProfile Fault Tolerance annotations
- '`@Retry`'
- '`@Fallback`'
- '`NullPointerException`'
- '`IllegalArgumentException`'
- '`onCascadeFailed`'
- folder loading
- materialized computation
- document update
- Failure path
- Bad request
- HTTP 400
- Retry
- Fallback
- Exception
- Java object
- Stale overwrite
- newer data
- older data
- Request fails
- Logs a warning
---

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

%% ai-graph-start %%

**Related notes:**
- [[02 Service Validation]]
- [[08 Glossary for Newbies]]
- [[05 Save and Side Effects]]
- [[03 Cascade Attempt]]
- [[document-put-cascade]]

**Relations:**
- document-put-cascade — *is_index_for* — Failure Paths
- Failure Paths — *preceded_by* — 05 Save and Side Effects
- Failure Paths — *followed_by* — 07 Files of Record
- PUT path — *can_fail_due_to* — Invalid document ID
- PUT path — *can_fail_due_to* — Body ID does not match path ID
- PUT path — *can_fail_due_to* — Version mismatch
- PUT path — *can_fail_due_to* — Deletion status changed
- PUT path — *can_fail_due_to* — Bad `folderIds` format
- PUT path — *can_fail_due_to* — Folder ID does not exist
- PUT path — *can_fail_due_to* — Client changed technical fields
- PUT path — *can_fail_due_to* — Materialize repository problem
- PUT path — *can_fail_due_to* — Materialize retries exhausted
- Visual explanation — *references* — diagrams/06-failure-paths.png
- Invalid document ID — *causes* — Request fails
- Body ID does not match path ID — *causes* — Request fails
- Version mismatch — *causes* — Request fails
- Deletion status changed — *causes* — Request fails
- Bad `folderIds` format — *causes* — Logs a warning
- Bad `folderIds` format — *throws* — `DocumentException`
- `DocumentException` — *has_status* — Bad Request Status
- Folder ID does not exist — *causes* — Request fails
- Client changed technical fields — *causes* — Request fails
- Materialize repository problem — *causes* — `MaterializeCascadeException`
- `MaterializeCascadeException` — *triggers_retry_behavior_in* — `onDocumentChange`
- Materialize retries exhausted — *throws* — `DocumentException`
- `DocumentException` — *contains_code* — `CAN_NOT_UPDATE_METADATA_IN_JSONSTORE`
- `DocumentException` — *has_http_status* — HTTP 500
- `MaterializeCascadeService.onDocumentChange` — *uses* — MicroProfile Fault Tolerance annotations
- MicroProfile Fault Tolerance annotations — *include* — `@Retry`
- MicroProfile Fault Tolerance annotations — *include* — `@Fallback`
- `@Retry` — *retries_on* — `MaterializeCascadeException`
- `@Retry` — *aborts_on* — `NullPointerException`
- `@Retry` — *aborts_on* — `IllegalArgumentException`
- `@Fallback` — *uses_method* — `onCascadeFailed`
- folder loading — *can_fail_with* — `MaterializeCascadeException`
- materialized computation — *can_fail_with* — `MaterializeCascadeException`
- Materialize retries exhausted — *leads_to* — document update
- document update — *fails_with_status* — HTTP 500
- Failure path — *is_a* — concept
- Failure path — *describes* — What the code does when something goes wrong
- Bad request — *is_a* — concept
- Bad request — *is_equivalent_to* — HTTP 400
- HTTP 400 — *indicates* — caller sent invalid data
- HTTP 500 — *indicates* — Server-side failure
- Retry — *is_a* — concept
- Retry — *is_defined_as* — Try the same operation again after a short delay
- Fallback — *is_a* — concept
- Fallback — *is_defined_as* — Backup method called when retries do not solve the problem
- Exception — *is_a* — concept
- Exception — *is_a* — Java object
- Exception — *represents* — an error
- Stale overwrite — *is_a* — concept
- Stale overwrite — *involves* — Accidentally replacing newer data with older data
- Stale overwrite — *replaces* — newer data
- Stale overwrite — *with* — older data

%% ai-graph-end %%