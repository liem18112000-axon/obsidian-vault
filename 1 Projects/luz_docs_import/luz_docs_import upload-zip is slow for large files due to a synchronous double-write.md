---
title: "luz_docs_import upload-zip is slow for large files due to a synchronous double-write"
created: 2026-08-12
type: observation
status: seedling
source: "docs/large-zip-upload-latency-investigation.md 2026-08-12"
tags: [luz-docs-import, performance, upload, LUZ-158230]
---

# luz_docs_import upload-zip is slow for large files due to a synchronous double-write

In `luz_docs_import`, `POST .../import-jobs/upload-zip` takes minutes to respond for ZIPs > 100 MB — but **not** because of import work. The import (virus scan, unzip, folder/document creation) is genuinely offloaded to an `@Asynchronous` EJB (`DocsImportAsyncService.importZipAndCleanFile`), so the slow part is the **synchronous ingest** on the request thread.

**Root cause:** the ZIP is written to disk **twice** before the 200 — resteasy-multipart buffers it to `/tmp` (`DocsImportService.java:112`), then `saveReferenceFileToTemp` copies it again to `/luz_docs_import/upload/...` (`:129`). Both are subPaths of the **same per-pod `pd-standard` 300 GiB VCT**, whose throughput is low — so ~1 GB of ZIP means ~a minute of serialized disk I/O plus the network-transfer floor. Validation only reads the ZIP central directory, so it is not the cost.

**Fix direction:** single-pass write (fold into the in-flight Filestore temp-storage plan, which currently keeps the double-write and moves the 2nd write onto slower NFS); ingress `proxy-request-buffering: off`; long-term pre-signed direct-to-GCS upload. Full write-up: `docs/large-zip-upload-latency-investigation.md` (docs/ is gitignored).

Related: [[RESTEasy MultipartFormDataInput buffers the whole upload to tmp before the app reads it]], [[GKE pd-standard disk throughput is low and scales with provisioned volume size]], [[Decouple upload API latency from file size with pre-signed direct-to-object-storage uploads]], [[nginx-ingress proxy-request-buffering stages the whole request body before forwarding to the pod]].

## Related

- [[RESTEasy MultipartFormDataInput buffers the whole upload to tmp before the app reads it]]
- [[GKE pd-standard disk throughput is low and scales with provisioned volume size]]
- [[Decouple upload API latency from file size with pre-signed direct-to-object-storage uploads]]
- [[nginx-ingress proxy-request-buffering stages the whole request body before forwarding to the pod]]
