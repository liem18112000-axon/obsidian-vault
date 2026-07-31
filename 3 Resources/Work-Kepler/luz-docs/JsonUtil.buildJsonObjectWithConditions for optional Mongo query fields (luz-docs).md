---
title: "JsonUtil.buildJsonObjectWithConditions for optional Mongo query fields (luz-docs)"
created: 2026-06-16
type: howto
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, mongodb, json, reuse, idiom]
---

# JsonUtil.buildJsonObjectWithConditions for optional Mongo query fields (luz-docs)

In luz-docs, build a Mongo query object with **optional** fields using `JsonUtil.buildJsonObjectWithConditions(Pair<Boolean, Pair<String,T>>...)` instead of hand-rolling `Json.createObjectBuilder` + if-guards. Each entry is `Pair.of(includeCondition, Pair.of(key, value))`; entries whose Boolean is false are dropped (and their possibly-null value never触touched). This is the same idiom `MongoDBUtil.buildFilterQuery` uses for its @Nullable as/limit params.

Example — an `_id` range clause where either bound may be absent (open-ended range):
```java
singletonJsonObject(Constants.ID, buildJsonObjectWithConditions(
    Pair.of(lower != null, Pair.of(Constants.MONGODB_OPERATOR_GREATER_THAN_EQUAL, lower)),
    Pair.of(upper != null, Pair.of(Constants.MONGODB_OPERATOR_LESS_THAN, upper))));
```

Companion builders in the same util family: `singletonJsonObject(key,value)`, `buildJsonObject(Pair...)` (unconditional), `buildJsonArray`/`toJsonArray`, and `MongoDBUtil.buildAndQuery/buildOrQuery/buildInQuery`. Prefer these over raw javax.json builders — keeps query construction consistent and avoids re-declaring $-operator literals (use `Constants.MONGODB_OPERATOR_*`).

## Related

- [[Parallelize visible-doc count by fan-out over _id ranges (luz-docs)]]
