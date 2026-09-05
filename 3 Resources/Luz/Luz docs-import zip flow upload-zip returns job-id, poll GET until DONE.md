---
title: "Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE"
created: 2026-08-10
type: howto
status: seedling
source: "session 2026-08-10 (luz-docs-import-api-test skill)"
tags: [luz, luz-docs-import, api, mongodb, import]
---

# Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE

The luz-docs-import zip-import flow is asynchronous, tracked by a job document:

1. **Upload** — `POST /luz_docs_import/api/{tenant}/import-jobs/upload-zip`, multipart with form field name **`file`** (constant `DocsImportService.FILE`). Returns an `ImportJob` JSON whose **`_id`** is the job-id — a **MongoDB `ObjectId`** (24-hex, e.g. `6a7943ee20d116293478d762`), NOT a UUID. Direct Mongo queries must coerce the string to `ObjectId`; the REST API accepts the hex string as-is.
2. **Poll** — `GET /luz_docs_import/api/{tenant}/import-jobs/{id}` until terminal. Add `?showWarning=true` to get the full enriched `ImportJob` (successfulFiles / skippedFilesDetail / rejectedFiles); default returns the master-compatible `LegacyImportJob` shape.
3. **Status** — `JobStatus` enum: `UPLOADED → SCANNING → DOCUMENT_CREATING → DONE`, or `FAILED`. Terminal = `DONE` / `FAILED`. On failure, `failureCode` ∈ {`INVALID`, `INFECTED`, `TIMEOUT`, `INTERNAL_SERVICE_ERROR`}.

**Persistence:** the job doc lives in the tenant Mongo collection **`document-import-jobs`** (constant shared by `JsonStoreService` + `IdempotentImportService`), stored with `_id` = the job-id `ObjectId`. Created folders/documents land in the tenant `folders` / `documents` collections. So progress can be validated two independent ways: the GET API, and a direct Mongo read of `document-import-jobs` + folder/doc counts (dev/dev-staging/performance).

**Auth:** the endpoint requires the `LUZ_DOCS_IMPORT` permission; a token lacking it → 403.

See the `luz-docs-import-api-test` skill for an end-to-end driver. Related: [[Trace Luz per-service latency via the time-consuming= log marker]].

## Related

- [[Trace Luz per-service latency via the time-consuming= log marker]]

**Deploy mapping (dev):** Cloud Build trigger = `docs-import-service`, k8s StatefulSet = `luz-docs-import`, GAR image = `luz-docs-import` (differs from the `luz-skill-ship` `luz-docs` defaults — pass `TRIGGER_NAME=docs-import-service STATEFULSET=luz-docs-import`).
