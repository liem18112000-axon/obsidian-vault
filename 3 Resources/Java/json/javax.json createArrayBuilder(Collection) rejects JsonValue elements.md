---
title: "javax.json createArrayBuilder(Collection) rejects JsonValue elements"
created: 2026-06-28
type: lesson
status: seedling
source: "session 2026-06-28"
tags: [java, json-p, jakarta, gotcha, luz-docs]
---

# javax.json createArrayBuilder(Collection) rejects JsonValue elements

In javax.json / Jakarta JSON-P (glassfish impl), `Json.createArrayBuilder(Collection)` and `Json.createObjectBuilder(Map)` are meant for collections of **plain Java values** (String, Number, Boolean, nested Map/List). They DEEP-CONVERT each element via `MapUtil.handle`, which does NOT accept already-built `JsonValue` instances — it throws `IllegalArgumentException: Type class org.glassfish.json.JsonStringImpl is not supported` when an element is (or contains) a JsonString/JsonObject.

So to build a `JsonArray` from a `Collection<JsonObject>` (or any JsonValue elements), do NOT use the collection-accepting factory. Instead add each via the builder, which has a typed `add(JsonValue)` overload:
```java
var b = Json.createArrayBuilder();
clauses.forEach(b::add);   // add(JsonValue) — correct
JsonArray arr = b.build();
```
Equivalently a varargs helper that maps each element through `Json::createValue`/identity works, but `createArrayBuilder(col)` / `createObjectBuilder(map)` on JsonValue contents fails.

Gotcha hit 2026-06-28 in luz_docs: `FullTextSearch.toQuery()` combined two `JsonObject` clauses with a homegrown `JsonUtil.toJsonArray(Collection)` (which delegates to `Json.createArrayBuilder(col)`), producing a 500 only on the two-clause path (term + rawQuery); single-clause paths skipped the array build so the bug hid until the combo request. Lesson: a util `toJsonArray(Collection<T>)` that wraps `createArrayBuilder(col)` is unsafe for JsonValue elements — only safe for raw Java values.

Relates to [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]].

## Related

- [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]]
