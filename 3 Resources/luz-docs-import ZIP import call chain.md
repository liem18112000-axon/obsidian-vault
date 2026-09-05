---
tags: [klara, luz-docs-import, luz-docs, architecture, integration]
created: 2026-08-24
---

# luz-docs-import ZIP import call chain

Source-verified high-level flow for a ZIP import (repos: `luz_docs_import`, `luz_docs_view_controller`, `luz_docs`).

1. **User → luz_docs_import** — `POST {tenant}/import-jobs/upload-zip`; runs as an EJB `@Asynchronous` background job (`DocsImportAsyncService.importZipAndCleanFile`).
2. **luz_docs_import → luz_antivirus = scans the METADATA file** (NOT the ZIP): `AntiviusScanningService.scanMetadataFile(...)` → `AntivirusRestClient POST /scanner`. This is the *first* scan.
3. **luz_docs_import → luz-docs-view-controller (REST)** — `LuzDocsViewControllerRestClient` `POST /{tenant}/documents` + `/archives/directories`, once per file/folder. luz_docs_import does NOT call luz-docs directly.
4. **view-controller → luz-docs** — VC's `LuzDocsCreateDocumentRestClient` → `http://luz-docs:8080/luz_docs/api POST /{tenant}/documents`.
5. **luz-docs → luz_antivirus = scans EACH BINARY file** (`AntivirusService.scan` in `DocumentCreatingService`). This is the *second* scan — so a ZIP import is "scanned twice" (metadata at import, each binary at luz-docs).
6. **luz-docs → Google Cloud Storage** = the file **content/binary** (encrypted, `GoogleCloudStorageService`).
7. **luz-docs → luz_jsonstore (MongoDB)** = the document + folder **records** (`jsonStoreMongoService.createDocumentMetadata`). Records + binaries together = the eArchive.
8. Result surfaces instantly in KLARA myLife storage.

Support services (not in the create-doc hot path): **jwt_service/luzsec** (`LuzSecRestClient`, auth); **luz_message_broker / Google Pub/Sub** (import-job status + notifications only — the import loop itself is the EJB async job, not Pub/Sub).

Gotcha: don't conflate the two stores — **binary → GCS**, **records → jsonstore/Mongo**. And step-2 AV target is the metadata file, not the archive. See [[KLARA dev login automation gotchas]].
