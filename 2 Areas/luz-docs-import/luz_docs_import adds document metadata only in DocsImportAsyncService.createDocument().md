---
title: "luz_docs_import adds document metadata only in DocsImportAsyncService.createDocument()"
created: 2026-08-04
type: observation
status: seedling
source: "LUZ-158230 investigation 2026-08-04"
tags: [luz-docs-import, architecture, kepler, metadata]
---

# luz_docs_import adds document metadata only in DocsImportAsyncService.createDocument()

In luz_docs_import, the ONLY place document metadata is assembled for a created document is DocsImportAsyncService.createDocument() (DocsImportAsyncService.java:406-428). It builds a MultipartFormDataOutput with parts `files`, `folderIds`, and a `metadata` JSON string, then POSTs to luz_docs_view_controller `POST /documents` (LuzDocsViewControllerRestClient.createDocument).

Today that `metadata` JSON carries only: `origin` ("ZIP-upload"), `originCreatedDate`, `originUpdatedDate`, and `documentTitle` (= filename without extension). To enrich a documents metadata (e.g. add healthData, senderName/TenantId/CompanyId, documentTypes, documentReferenceDate for the LUZ-158230 health import), this single method is the injection point — but the DOWNSTREAM view-controller + document store must also persist and return the new fields; luz_docs_import only forwards them.

**Why it matters:** one small, testable change point; and it makes the "no regression" guarantee easy — emit the extra fields only when a sidecar is present, otherwise the payload is byte-for-byte todays.

Related: [[Health ZIP import broken sidecar still imports; orphan sidecar is the only rejection]]

## Related

- [[Health ZIP import broken sidecar still imports; orphan sidecar is the only rejection]]
