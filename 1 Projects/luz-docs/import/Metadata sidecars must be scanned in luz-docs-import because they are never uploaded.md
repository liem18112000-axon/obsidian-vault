---
title: "Metadata sidecars must be scanned in luz-docs-import because they are never uploaded"
created: 2026-08-10
type: argument
status: seedling
source: "session 2026-08-10, LUZ-158230"
tags: [luz-docs-import, antivirus, earchive, LUZ-158230, design-decision]
---

# Metadata sidecars must be scanned in luz-docs-import because they are never uploaded

In the eArchive ZIP import (LUZ-158230), the antivirus responsibility is split by *who uploads the bytes*.

- **Document payloads** (pdf/docx/images) are scanned **downstream** by the luz-docs view-controller when `LuzDocsViewControllerService.createDocument` uploads them. So this import service does **not** scan them itself.
- **Metadata sidecars** (`<doc>.metadata.json`) are **never uploaded** to the view-controller — their fields are folded locally into the document metadata JSON via `HealthDocImporter.applyMetadata`. Therefore `luz-docs-import` is the *only* place a sidecar can be scanned before its contents are trusted.

Consequence: the old upfront whole-zip scan in `DocsImportAsyncService.importZipAndCleanFile` was removed as redundant for documents, and a dedicated per-sidecar scan was added instead. If the downstream view-controller ever stops scanning on upload, document payloads would go unscanned — that assumption is load-bearing.

See [[luz-docs-import scans metadata sidecars per-file, not the whole ZIP]] and [[Per-file AV scan rejects one file; whole-job scan fails the whole import]].

## Related

- [[luz-docs-import scans metadata sidecars per-file]]
- [[not the whole ZIP]]
- [[Per-file AV scan rejects one file; whole-job scan fails the whole import]]
