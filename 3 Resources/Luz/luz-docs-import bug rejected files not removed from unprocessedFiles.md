---
title: "luz-docs-import bug: rejected files not removed from unprocessedFiles"
created: 2026-08-10
type: lesson
status: seedling
source: "session 2026-08-10 (transfer.zip test run)"
tags: [luz, luz-docs-import, bug, import, gotcha]
---

# luz-docs-import bug: rejected files not removed from unprocessedFiles

In luz-docs-import, `ImportJob.unprocessedFiles` must contain only files that reached NO terminal decision. It is seeded with the full non-metadata candidate set at `DocsImportAsyncService.createFolderAndDocuments` (~line 121), and every terminal branch removes the file from it: success (`createNewDocuments`, ~line 150), failure (`JobProgressWriter.recordFailure`, line 46), skipped/dedup (`JobProgressWriter.recordSkippedAll`, line 61).

**BUG:** the unsupported-file-type rejection branch in `processDocumentFile` (~lines 170-177) adds to `rejectedFiles` and `return`s WITHOUT `job.getUnprocessedFiles().remove(filePath)`. So rejected files (e.g. .csv/.exe/nested .zip) wrongly linger in `unprocessedFiles` — appearing in BOTH `rejectedFiles` and `unprocessedFiles`.

**Tell:** if `rejected=N` but `unprocessedFiles` has fewer than N entries, the difference is orphan JSON-metadata rejects — metadata files are excluded from `unprocessedFiles` at seed time (`!JsonUtils.isJsonMetadataFile`), so only the non-metadata file-type rejects leak.

**Fix:** add `job.getUnprocessedFiles().remove(filePath)` in the file-type-reject branch (line ~173), same as the other terminal paths. Found via the luz-docs-import-api-test skill run on transfer.zip. Related: [[Luz docs-import zip flow: upload-zip returns job-id, poll GET until DONE]].

## Related

- [[Luz docs-import zip flow: upload-zip returns job-id]]
- [[poll GET until DONE]]
