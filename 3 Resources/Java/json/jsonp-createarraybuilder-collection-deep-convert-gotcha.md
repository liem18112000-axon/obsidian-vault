---
title: JSON-P Json.createArrayBuilder(Collection) deep-converts and chokes on nested pre-built JsonNumber
tags: [java, json-p, glassfish, jsonstore, luz, gotcha]
created: 2026-07-16
type: resource
---

# `Json.createArrayBuilder(Collection)` fails on nested pre-built `JsonValue` numbers

## Symptom
```
java.lang.IllegalArgumentException: Type class org.glassfish.json.JsonNumberImpl$JsonLongNumber is not supported.
    at org.glassfish.json ... 
    at JsonUtil.getJSAB (Json.createArrayBuilder(col))
    at JsonUtil.singletonJsonArray
```
Thrown at runtime when building a Mongo update pipeline whose `$set` expression contained a nested number (`SHARD_SPACE = 1<<30`).

## Cause
glassfish `Json.createArrayBuilder(Collection)` / `createObjectBuilder(Map)` **deep-copy** their input and re-run each element through an internal type handler. That handler accepts raw Java types (String/Number/Boolean/Map/Collection) and *top-level* JsonValues, but blows up on an already-built `JsonNumber` subtype (`JsonLongNumber`) sitting **nested** inside the structure.

`luz_docs` `JsonUtil.singletonJsonArray(x)` → `toJsonArray(List.of(x))` → `getJSAB(col)` → `Json.createArrayBuilder(col)` — so wrapping a pre-built JsonObject (that transitively holds a JsonLongNumber) via `singletonJsonArray` triggers it.

## Fix / rule
Build arrays that will hold pre-constructed JsonValues with the **builder `.add(JsonValue)` path**, not the Collection constructor:
```java
// BAD  — deep-converts, dies on nested JsonLongNumber
singletonJsonArray(setStageJsonObject)
// GOOD — .add(JsonValue) stores as-is, no re-conversion
Json.createArrayBuilder().add(setStageJsonObject).build()
```
`JsonUtil.buildJsonArray(T...)` and `toJsonArray(T[])` are also safe — they map each element through `toJsonValue` and `builder.add(...)`, no deep Collection copy. Only the **Collection/Map** JSON-P constructors deep-convert.

## Extra gotcha it caused
When `shardAssignmentBody()` threw here, `ParallelizeMigrationExecutor.execute()` blew up *before* calling `mdb.updateMany(...)`, so the Mockito test's `doThrow(...).when(mdb).updateMany(...)` stub went unused → surfaced as a misleading `UnnecessaryStubbingException` rather than the real error. When strict-stubbing complains about a stub you know is right, check whether the code throws earlier.

## Confirmed in-container (not just unit-test classpath)
Reproduced inside the running `luz-docs` WildFly pod against its own module jar `jakarta.json-1.1.6` (glassfish `org.glassfish.json` impl): `Json.createArrayBuilder(List.of(setStageWithNestedLong)).build()` → `THREW: IllegalArgumentException: Type class org.glassfish.json.JsonNumberImpl$JsonLongNumber is not supported.` So the bug is real at runtime; a mockStatic-the-builder test would hide a genuine prod break. Verify provider behavior in-container (write repro to `/tmp`, `javac -cp <module-jar>`, run) rather than trusting the unit-test classpath alone — build dep was `org.glassfish:javax.json:1.1`, container ships `1.1.6`; both throw.

Related: [[parallelize-old-perdoc-vs-new-oneshot-in-logs]]
