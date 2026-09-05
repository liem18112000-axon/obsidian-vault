---
title: "luz_jsonstore V2 BSON endpoints must be Document-in Document-out"
created: 2026-08-26
type: lesson
status: seedling
source: "session 2026-08-26"
tags: [luz-jsonstore, bson, resteasy, jax-rs, gotcha]
---

# luz_jsonstore V2 BSON endpoints must be Document-in Document-out

Every REST endpoint in **luz_jsonstore V2** (`JsonStoreMongoDbResourceV2`, class-level `@Consumes/@Produces application/bson`) must take a raw `org.bson.Document` as its request body and return a `Document` as its entity. Nothing else serializes.

**Why:** the only provider registered for `application/bson` is `BsonProvider`, which is hardwired to `Document` — it `implements MessageBodyReader<Document>, MessageBodyWriter<Document>`; `readFrom` always returns `BsonUtil.decode(in)` (a `Document`), and `writeTo` only accepts a `Document`. On WildFly 26 / RESTEasy, providers are pre-filtered by their generic type parameter, so `BsonProvider` is not even eligible for other types, and **Jackson is not invoked for `application/bson`**.

**Consequences when porting V1 endpoints to V2:**
- POJO request bodies (e.g. `UpdateManyBody`, `UpdateBulkBody`) cannot be deserialized — accept a `Document` and read out the `filter` / `update` fields inside the method.
- Non-`Document` return types (`UpdateResult`, `BulkWriteResult`, `DeleteResult`, `long`) cannot be serialized — wrap results in a counts `Document`, e.g. `new Document("matchedCount", r.getMatchedCount()).append("modifiedCount", r.getModifiedCount())`.

See [[luz_jsonstore committed V2 updateOne count delete have latent BSON serialization bug]] for the endpoints already violating this.

**Corollary — collections/arrays must be wrapped:** a *bare* BSON array is not a valid top-level BSON document, so any endpoint that logically takes or returns a list must wrap it in a `Document`. In the ported V2 endpoints: `addMany` body is `{ documents: [...] }`, `aggregate` body is `{ pipeline: [...], collation: {...} }`, and both `getMany` (`POST .../{collection}/query`) and `aggregate` respond with `{ documents: [...] }`. `List<Document>` as a `Document` *value* encodes fine via `DocumentCodec`; it just can't be the top-level entity.

**Design decision — BSON-native query spec, no string parsing:** V1 `getMany` took `sort`/`skip`/`limit`/`project`/`collation` as query-param **strings** and rebuilt them with `Document.parse("{"+sort+"}")` + Gson. V2 instead takes one BSON spec document `{ filter, sort, projection, skip, limit, collation }` with native types (ints for skip/limit, sub-docs for sort/projection). Collation is rebuilt from a sub-document via a `Collation.builder()` mapper.

**Design decision — V2 drops V1's `convertToObjectId` / `tempAdjustFormat`:** V1 coerced string `_id`s to `ObjectId` and reformatted date strings on the way in/out because its clients spoke JSON. V2 consumes/produces BSON, which already carries native `ObjectId` and `Date` types — so the coercion is unnecessary by construction. This is the whole point of the BSON migration (branch `migrate-dates-as-string-to-mongo-db-date`).

**Now enforced (session 2026-08-26):** `BsonProvider.isReadable`/`isWriteable` were tightened from a media-type-only check to also require `Document.class.isAssignableFrom(clazz)`. The provider now honestly claims only `Document`, so a non-Document entity is rejected at provider selection instead of throwing a `ClassCastException` inside `writeTo`. Pattern adopted: the service returns native driver types and the **resource** folds them into a counts `Document` via private `toDocument(...)` overloads. See also [[JAX-RS literal path segment outranks a template variable in request matching]] for how the new `update-bulk` / `{doc-id}` PATCH endpoints coexist.

## Related

- [[luz_jsonstore committed V2 updateOne count delete have latent BSON serialization bug]]
- [[JAX-RS literal path segment outranks a template variable in request matching]]
