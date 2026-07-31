---
title: Json.createArrayBuilder(Collection) deep-converts elements (glassfish JSON-P 1.1.6)
tags: [java, json-p, jsonp, glassfish, gotcha, luz-docs]
created: 2026-06-19
---

# Json.createArrayBuilder(Collection) deep-converts — throws on JsonValue elements

## The trap
In glassfish JSON-P (`org.glassfish.json`, jakarta json 1.1.6), `Json.createArrayBuilder(Collection)`
does **not** simply add the collection elements. It walks each element through `MapUtil.handle`,
which only understands **raw Java types** (String, Number, Boolean, Map, Collection). When an element
is an already-built `JsonValue` (e.g. a `JsonObject` whose field values are `JsonStringImpl`), it
recurses into the map and throws:

```
java.lang.IllegalArgumentException: Type class org.glassfish.json.JsonStringImpl is not supported.
  at org.glassfish.json.MapUtil.handle(MapUtil.java:88)
  at org.glassfish.json.JsonObjectBuilderImpl.populate(...)
  at org.glassfish.json.JsonArrayBuilderImpl.populate(...)
  at javax.json.Json.createArrayBuilder(Json.java:266)
```

The luz_docs helper `JsonUtil.toJsonArray(Collection)` → `getJSAB(col)` → `Json.createArrayBuilder(col)`
hits this when fed a `List<JsonObject>` of real documents.

## The fix
Add each `JsonValue` directly — these paths add as-is, no deep conversion:
- `JsonArrayBuilder.add(JsonValue)` per element (`Json.createArrayBuilder(); rows.forEach(b::add)`)
- or a stream collector: `stream.collect(javax.json.stream.JsonCollectors.toJsonArray())`

## Where it bit
`ParallelizeMerge.mergePage` (materialize parallel search). A "max reuse" refactor swapped the
explicit per-element builder for `JsonUtil.toJsonArray(rows)` and every fan-out search (K>=2)
500'd with the error above — fan-out sub-searches themselves were fine (all returned 200).

**Lesson:** reusing a project util is only safe if the util's element-handling matches your element
type. A `Collection<JsonObject>` is NOT interchangeable with a `Collection<rawJavaType>` for
`createArrayBuilder(Collection)`. See [[luz-materialize-parallel-search]].
