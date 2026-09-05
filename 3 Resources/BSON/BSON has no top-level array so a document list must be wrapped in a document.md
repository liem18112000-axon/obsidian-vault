---
title: "BSON has no top-level array so a document list must be wrapped in a document"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24"
tags: [bson, mongodb, gotcha]
---

# BSON has no top-level array so a document list must be wrapped in a document

BSON has no top-level array type; the root of a BSON payload is always a document. To ship a List<Document> over an application/bson endpoint, wrap it in a single BSON document, e.g. { "items": [ {...}, {...} ] }, and unwrap the "items" field on read.

A mongo-java-driver DocumentCodec round-trips this cleanly: nested BSON arrays decode to java.util.List and embedded documents decode to org.bson.Document, so wrapper.get("items") yields a List<Document>.

## Related

- [[Serving a custom application/bson media type in JAX-RS via MessageBodyReader and Writer]]
