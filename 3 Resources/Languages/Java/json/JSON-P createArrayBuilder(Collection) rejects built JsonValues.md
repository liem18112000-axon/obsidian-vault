---
title: "JSON-P createArrayBuilder(Collection) rejects built JsonValues"
created: 2026-06-19
type: gotcha
status: seedling
tags: [java, json-p, jsonp, jakarta-json, javax-json, glassfish, gotcha, luz-docs]
entities: [Json.createArrayBuilder, Json.createObjectBuilder, MapUtil.handle, JsonArrayBuilder.add, JsonCollectors.toJsonArray, org.glassfish.json, JsonStringImpl, JsonNumberImpl$JsonLongNumber, JsonUtil.toJsonArray, JsonUtil.getJSAB, JsonUtil.singletonJsonArray]
---

# JSON-P `createArrayBuilder(Collection)` rejects already-built JsonValues

`Json.createArrayBuilder(Collection)` and `Json.createObjectBuilder(Map)` (glassfish `org.glassfish.json`, jakarta.json 1.1.x) **deep-convert** their input instead of adding it as-is. Each element is re-run through `MapUtil.handle`, which only understands **raw Java types** (String, Number, Boolean, Map, Collection). An already-built `JsonValue` — at the top level or **nested** anywhere inside — throws at runtime:

```
java.lang.IllegalArgumentException: Type class org.glassfish.json.JsonStringImpl is not supported.
  at org.glassfish.json.MapUtil.handle(MapUtil.java:88)
  at javax.json.Json.createArrayBuilder(Json.java:266)
```

Same failure with `JsonNumberImpl$JsonLongNumber` for a nested pre-built number. Compiles fine; only fails at runtime, and only on the code path that actually builds the array.

## Fix

Add each value one at a time — `JsonArrayBuilder.add(JsonValue)` stores as-is, no re-conversion:

```java
JsonArrayBuilder out = Json.createArrayBuilder();
builtObjects.forEach(out::add);   // add(JsonValue) — fine
return out.build();

// NOT: Json.createArrayBuilder(builtObjects).build();  // throws
```

`javax.json.stream.JsonCollectors.toJsonArray()` is the stream equivalent. Varargs/array helpers that map each element through `toJsonValue` + `builder.add(...)` (`JsonUtil.buildJsonArray(T...)`, `toJsonArray(T[])`) are also safe — only the **Collection/Map** constructors deep-convert.

## Rule

A util like `JsonUtil.toJsonArray(Collection)` → `getJSAB(col)` → `createArrayBuilder(col)` is safe **only for raw Java values**. `Collection<JsonObject>` is not interchangeable with `Collection<rawJavaType>`. Reusing a project util is only safe if its element handling matches your element type.

Bit luz_docs repeatedly: `ParallelizeMerge.mergePage` (fan-out K>=2 → 500), `KeyValueMerger.merge` (500 on `/documents/search`), `FullTextSearch.toQuery()` (500 only on the two-clause term+rawQuery path), `JsonUtil.singletonJsonArray` wrapping a `$set` stage holding `SHARD_SPACE = 1<<30`.

Verified in-container on the WildFly pod's own `jakarta.json-1.1.6` module jar, not just the unit-test classpath (build dep was `org.glassfish:javax.json:1.1`; both throw) — a `mockStatic`-the-builder test would hide a genuine prod break.

Side gotcha: when this throws *before* a stubbed collaborator call, Mockito strict stubbing reports a misleading `UnnecessaryStubbingException` instead of the real error.

## Related

- [[luz-materialize-parallel-search]]
- [[parallelize-old-perdoc-vs-new-oneshot-in-logs]]
- [[luz-docs raw-mongo search passthrough uses an operator whitelist for security parity with the DSL]]
