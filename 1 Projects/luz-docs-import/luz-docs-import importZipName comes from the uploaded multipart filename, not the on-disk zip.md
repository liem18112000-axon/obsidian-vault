---
title: "luz-docs-import importZipName comes from the uploaded multipart filename, not the on-disk zip"
created: 2026-08-13
type: observation
status: seedling
source: "session 2026-08-13"
tags: [luz-docs, idempotency, import, multipart, testing]
---

# luz-docs-import importZipName comes from the uploaded multipart filename, not the on-disk zip

In `luz_docs_import`, the idempotency key `importZipName` is derived from the **uploaded multipart part filename**, not from the fixture file name on disk.

Chain: `DocsImportService.saveReferenceFileToTemp` takes `FileMultipartUtil.getFileName(inputPart)` (the multipart part's `filename=`), writes the temp zip under that name, and `importZipFile` sets `importJob.setImportZipName(FilenameUtils.removeExtension(tmpZipFile.getName()))`. `IdempotentImportService` then dedupes on `importZipName` + each file's relative path.

**Consequences for testing re-import / dedupe:**
- To make a second upload be **skipped**, it must be POSTed with the **same multipart `filename`** as the first — even if the on-disk fixtures differ. (Fixture `34-edge-reimport-changed-body.zip` must be uploaded *as* `15-edge-duplicates-upload-twice.zip`; the two `36*` collision sets must both be uploaded *as* e.g. `documents.zip`.)
- Renaming the upload (same bytes, new `filename`) makes it a **different** `importZipName` → not deduped → re-imported (`35-edge-reimport-renamed-zip.zip`).
- Dedupe is path-based within an `importZipName`; content/size are ignored (see [[luz-docs-import AV scan covers only the metadata sidecar, never the document binary]] repo).

**Lesson:** when a dedupe/idempotency key is sourced from a client-supplied field (the multipart filename), the *client controls the key* — a test harness sets it via the upload, and two genuinely different payloads sent under one filename will cross-dedupe.
