---
title: "luz-docs-import AV scan covers only the metadata sidecar, never the document binary"
created: 2026-08-13
type: observation
status: seedling
source: "session 2026-08-13"
tags: [luz-docs, antivirus, security, import, gap]
---

# luz-docs-import AV scan covers only the metadata sidecar, never the document binary

In `luz_docs_import`, the antivirus scan during ZIP import only ever scans the `.metadata.json` **sidecar**, not the document binary itself.

`DocsImportAsyncService.processDocumentFile` calls `antiviusScanningService.scanMetadataFile(metadataFile, token)` **only when `getMetadataJsonFile(file).isFile()`** is true. The document file (`.pdf`, `.png`, etc.) is never passed to the AV service.

**Consequences:**
- A document with **no sidecar** is imported with **zero AV scanning**.
- An **infected document body** with a clean (or absent) sidecar is not caught.
- When the *sidecar* is INFECTED, the whole **document** is rejected (`recordRejected`, "Virus found in the metadata file"), even though the document binary was the thing never scanned.

Scan outcomes and where they route the document: `INFECTED` -> rejectedFiles; `TIMEOUT` (30s) / `TECHNICAL_ERROR` -> failedFiles; `CLEAN` -> proceed.

**Why it matters:** if the security intent is "no infected document reaches storage", this design does not achieve it — only sidecar JSON is scanned. Flag for the spec owner; likely a gap, not a deliberate decision. See [[RESTEasy multipart repeated field name yields a List, get(0) silently drops extras]] for the same repo.
