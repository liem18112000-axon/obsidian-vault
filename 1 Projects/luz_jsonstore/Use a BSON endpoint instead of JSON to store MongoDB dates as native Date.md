---
title: "Use a BSON endpoint instead of JSON to store MongoDB dates as native Date"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24"
tags: [mongodb, bson, luz-jsonstore, date]
---

# Use a BSON endpoint instead of JSON to store MongoDB dates as native Date

Transporting documents as JSON loses type fidelity. The luz_jsonstore v1 endpoints serialized org.bson.Document via Document.toJson(), so a date field arrived as a string and was stored in MongoDB as a string. Switching the wire format to BSON fixes this end-to-end: a BSON datetime decodes to java.util.Date and is stored as a native Mongo Date.

The same fidelity argument applies to _id: BSON carries a native ObjectId, so the v1 setDocId() step that rewrote _id into a hex string is intentionally dropped in v2 (JsonStoreMongoDbResourceV2 at path mdb/v2/{tenant-id}, reusing the same JsonStoreMongoDbService). This is the core rationale behind the "dates as string -> Mongo Date" migration branch.

## Related

- [[Serving a custom application/bson media type in JAX-RS via MessageBodyReader and Writer]]
- [[BSON has no top-level array so a document list must be wrapped in a document]]
