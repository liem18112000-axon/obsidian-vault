---
title: "A JAX-RS body param typed org.bson.Document is JSON-deserialized, not a BSON wire format"
created: 2026-07-29
type: lesson
status: seedling
source: "luz_jsonstore session 2026-07-29"
tags: [jax-rs, mongodb, bson, serialization, gotcha]
---

# A JAX-RS body param typed org.bson.Document is JSON-deserialized, not a BSON wire format

When a JAX-RS resource method parameter is typed as `org.bson.Document` (the MongoDB Java driver's map-like type), it does **not** mean the endpoint speaks BSON. The framework's JSON provider (JSON-B / Jackson) parses the JSON request body and constructs the `Document` in memory; results are written back as JSON via `document.toJson()`. So `org.bson.Document` here is an **in-memory data model**, not a BSON wire format.

Why it matters: seeing `org.bson.*` types (`Document`, `Bson`, `ObjectId`) all over a resource can fool you into thinking it accepts `application/bson`. It doesn't. To genuinely accept BSON bytes on the wire you need **both**:
1. `@Consumes("application/bson")` (added to the method or class list), and
2. a custom JAX-RS `@Provider` implementing `MessageBodyReader<T>` for `application/bson` that decodes the raw `InputStream` bytes (e.g. `RawBsonDocument` / `BsonBinaryReader` + the driver codec registry) into the target type.

Concrete example: luz_jsonstore's `JsonStoreMongoDbResource` has many endpoints taking `Document` / `List<Document>` bodies, yet all are JSON-only — no BSON reader is registered.

Related: [[OpenAPI @RequestBody mediaType is documentation-only; JAX-RS @Consumes controls content negotiation]]

## Related

- [[OpenAPI @RequestBody mediaType is documentation-only; JAX-RS @Consumes controls content negotiation]]
