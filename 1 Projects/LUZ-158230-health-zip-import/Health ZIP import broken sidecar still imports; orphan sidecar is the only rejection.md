---
title: "Health ZIP import broken sidecar still imports; orphan sidecar is the only rejection"
created: 2026-08-04
type: lesson
status: seedling
source: "SPEC v1.0 / BRD v0.3 2026-08-04"
tags: [luz-docs-import, kepler, LUZ-158230, import-rules]
---

# Health ZIP import broken sidecar still imports; orphan sidecar is the only rejection

The ePost health ZIP import (transfer.zip, LUZ-158230) has three tolerant-handling rules that are easy to get wrong:

- **BRule-4** — an UNPARSEABLE `<filename>.metadata.json` sidecar is ignored *entirely* and the document is STILL imported (with filename as title). A broken sidecar must never cost the user the document.
- **BRule-5** — a sidecar with NO matching sibling document is the ONLY entry-level rejection in the whole spec. Everything else imports.
- **BR-05** — no field is mandatory and there is NO schema validation. Unrecognised top-level keys are SILENTLY ignored (this is what protects internal system properties from being overwritten by a sender). Model healthData as an opaque passthrough subtree, not a rigid POJO, so extra languages/keys are not dropped.

Sidecar recognition (BR-03) is by exact name only: complete document filename incl. extension + lowercase `.metadata.json`, same folder. healthData is present when `documentTypes` contains `HEALTH` (BR-07).

Related: [[luz_docs_import adds document metadata only in DocsImportAsyncService.createDocument()]] [[luz_docs_import isExcludedFile misses Thumbs.db and __MACOSX (BR-04 gap)]]

## Related

- [[luz_docs_import adds document metadata only in DocsImportAsyncService.createDocument()]]
- [[luz_docs_import isExcludedFile misses Thumbs.db and __MACOSX (BR-04 gap)]]
