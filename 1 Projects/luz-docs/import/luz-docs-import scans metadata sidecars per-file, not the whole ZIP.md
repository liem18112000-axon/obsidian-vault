---
title: "luz-docs-import scans metadata sidecars per-file, not the whole ZIP"
created: 2026-08-10
type: howto
status: seedling
source: "session 2026-08-10, LUZ-158230"
tags: [luz-docs-import, antivirus, earchive, LUZ-158230]
---

# luz-docs-import scans metadata sidecars per-file, not the whole ZIP

As of LUZ-158230, `DocsImportAsyncService` no longer scans the uploaded ZIP as a whole. The `antiviusScanningService.scanUploadFile(zipFile, token)` call was removed from `importZipAndCleanFile`.

Instead, in `processDocumentFile`, right before a documents metadata is read, the sidecar is scanned once:

```java
File metadataFile = JsonUtils.getMetadataJsonFile(file);
if (metadataFile.isFile() && !antiviusScanningService.isFileClean(metadataFile, token)) {
    progress.recordRejected(file, "Virus found in the metadata file");
    return;
}
```

The scan happens **once, before** `MetadataResult.parseJsonMetadataFromFile` reads/parses the JSON — so nothing derived from an unscanned sidecar is ever trusted. Each document has exactly one sidecar and is processed once, so "scan once before reading" holds naturally.

Why not the whole-zip scan anymore: see [[Metadata sidecars must be scanned in luz-docs-import because they are never uploaded]]. Why a bad scan only rejects that one document: see [[Per-file AV scan rejects one file; whole-job scan fails the whole import]].

## Related

- [[Metadata sidecars must be scanned in luz-docs-import because they are never uploaded]]
- [[Per-file AV scan rejects one file; whole-job scan fails the whole import]]
