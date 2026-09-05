---
title: "Decouple upload API latency from file size with pre-signed direct-to-object-storage uploads"
created: 2026-08-12
type: technique
status: seedling
source: "luz_docs_import large-ZIP latency investigation 2026-08-12"
tags: [upload, api-design, gcs, s3, presigned-url, performance, architecture]
---

# Decouple upload API latency from file size with pre-signed direct-to-object-storage uploads

An HTTP endpoint that accepts a file **as the request body** cannot send its response until the whole body has been received — so the response latency is **floored by upload-transfer time** and grows with file size, no matter how fast the server-side processing is. Making the processing async does not help the *upload* call itself.

**Pattern to decouple latency from size:** issue the client a **pre-signed URL** to object storage (GCS/S3); the client uploads the bytes **directly to storage**, never through the app pod; then it calls the API with just the **object reference**. The API response is now constant-time regardless of file size, and a worker streams the object from storage for processing.

Alternatives when the file must traverse the API: resumable/chunked upload (e.g. tus, GCS resumable) so a flaky link survives and the client can show progress instead of blocking on one long POST. Cheapest of all: if the *processing* is already async, the only thing blocking is the client waiting on the upload — a progress UI + generous timeout may be enough without any backend change.

Related: [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]].

## Related

- [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]]
