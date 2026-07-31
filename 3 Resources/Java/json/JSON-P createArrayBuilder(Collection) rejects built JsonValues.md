---
tags: [java, jsonp, jakarta-json, gotcha]
created: 2026-06-26
---

# JSON-P `createArrayBuilder(Collection)` rejects already-built JsonValues

`Json.createArrayBuilder(Collection)` / `createObjectBuilder(Map)` (glassfish `org.glassfish.json`) **deep-copy** their input, expecting **raw Java values** (String, Number, Boolean, Map, List). They recurse via `MapUtil.handle`, which throws:

```
java.lang.IllegalArgumentException: Type class org.glassfish.json.JsonStringImpl is not supported.
```

when an element is an **already-built `JsonValue`** (e.g. a `JsonObject` whose fields are `JsonStringImpl`). It only knows raw types, not the JSON-P value types it produced itself.

## Fix
To assemble an array from built `JsonObject`/`JsonValue` elements, **add them one at a time** — `JsonArrayBuilder.add(JsonValue)` accepts built values:

```java
JsonArrayBuilder out = Json.createArrayBuilder();
builtObjects.forEach(out::add);   // add(JsonValue) — fine
return out.build();
```

NOT:
```java
Json.createArrayBuilder(builtObjects).build();   // throws on JsonStringImpl
```

## Where it bit
luz_docs `KeyValueMerger.merge` (facet fan-out): used `JsonUtil.toJsonArray(List<JsonObject>)` → `createArrayBuilder(col)` → 500 on `/documents/search`. Compiles fine; only fails at runtime when the list holds built JsonObjects. `JsonUtil.toJsonArray(Collection)` is safe only for collections of raw values.
