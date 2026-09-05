---
title: "luz-jsonstore GET-by-id masks read exceptions as empty-body 400; 10MB max-post-size caps whole-doc $set writes"
created: 2026-08-07
type: lesson
status: seedling
source: "luz_jsonstore trace 2026-08-07"
tags: [jsonstore, luz-docs-import, error-handling, mongodb, gotcha]
---

# luz-jsonstore GET-by-id masks read exceptions as empty-body 400; 10MB max-post-size caps whole-doc $set writes

luz-jsonstore's mdb GET-by-id handler (JsonStoreMongoDbResource GET {id}) wraps the read in `try { ... } catch (Exception e) { LOG.SEVERE(tenant+" getOne", e); } return Response.status(BAD_REQUEST).build();` — so **any** exception during getOne/setDocId/doc.toJson() becomes an **HTTP 400 with an EMPTY body**. No ExceptionMapper is registered (all commented out in BaseApplicationPath), so the real cause is logged **server-side only** (SEVERE "<tenant> getOne") and is invisible to callers.

Consequence for luz_docs_import: its `DocsResponseExceptionMapper` (handles status>=300) turns that bodyless 400 into a detail-less `DocsException` -> UnexpectedExceptionMapper -> HTTP 500 with a blank message. So a 'GET import-job 500 with empty message' means 'jsonstore getOne threw something' — go read jsonstore's SEVERE getOne log for the real reason; the empty body is by design.

There is NO response/document read-size cap in luz-jsonstore. The only size limit is REQUEST_SIZE_LIMIT_IN_BYTES=10MB -> Undertow max-post-size (Dockerfile / http-listener.xml) which caps INBOUND request bodies (writes), not GET responses. Mongo's hard limit is 16MB BSON. So multi-MB reads are not capped; a read 400 is a transient getOne exception, not a size rejection.

Write-path corollary (real latent bug): JsonStoreService.updateJob PATCHes the ENTIRE growing ImportJob as {$set: job} on every throttled flush. Once the job doc approaches ~10MB (e.g. 100k successfulFiles entries), the PATCH request body exceeds max-post-size (10MB) and the write is rejected -> job persistence fails mid-import. Fix: bound the job doc (store counts + only warning entries, not one successfulFiles entry per file) — this also removes any giant-read pressure.

Related: [[luz_docs_import]], [[Merging guard-with-return into && drops the unconditional skip]].

## Related

- [[luz_docs_import]]
