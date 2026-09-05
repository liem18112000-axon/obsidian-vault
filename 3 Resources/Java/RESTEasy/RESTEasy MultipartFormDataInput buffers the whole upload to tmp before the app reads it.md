---
title: "RESTEasy MultipartFormDataInput buffers the whole upload to tmp before the app reads it"
created: 2026-08-12
type: gotcha
status: seedling
source: "luz_docs_import large-ZIP latency investigation 2026-08-12"
tags: [resteasy, multipart, jaxrs, upload, gotcha, performance]
---

# RESTEasy MultipartFormDataInput buffers the whole upload to tmp before the app reads it

RESTEasy's `MultipartFormDataInput` fully reads and **buffers each incoming multipart part to disk** (`java.io.tmpdir`, default `/tmp`) while parsing the form — before your handler ever touches `inputPart.getBody(InputStream.class)`. So by the time you read the stream, the upload is already on disk once.

**Why it bites:** if the handler then copies that stream to its *own* temp path (e.g. `FileUtils.copyInputStreamToFile`), the file is written to disk **twice** for one upload — a full-size read + write on top of the framework buffer. For large uploads on slow storage this doubles the on-thread I/O before any response is sent.

**Fixes:** (a) since the framework already wrote it, `Files.move` the backing temp file to the destination if it is on the same filesystem (O(1) rename) instead of copying; or (b) bypass the buffered `MultipartFormDataInput` and stream the raw request body once to the final path. Also note the part is buffered to `/tmp`, so `/tmp` must have room for the largest allowed upload (`MAX_POST_SIZE`).

Related: [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]], [[GKE pd-standard disk throughput is low and scales with provisioned volume size]].

## Related

- [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]]
- [[GKE pd-standard disk throughput is low and scales with provisioned volume size]]
