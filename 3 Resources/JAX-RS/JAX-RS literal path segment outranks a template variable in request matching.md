---
title: "JAX-RS literal path segment outranks a template variable in request matching"
created: 2026-08-26
type: concept
status: seedling
source: "session 2026-08-26"
tags: [jax-rs, resteasy, routing, rest]
---

# JAX-RS literal path segment outranks a template variable in request matching

In JAX-RS (RESTEasy on WildFly), when two resource methods share an HTTP verb and their paths differ only in that one segment is a **literal** and the other is a **template variable**, the request-matching algorithm ranks the literal as more specific and picks it. So a literal sub-resource path can safely coexist with a catch-all `{id}` path on the same verb.

Concrete case in `JsonStoreMongoDbResourceV2` (class path `mdb/v2/{tenant-id}/{collection}`), all under `@PATCH`:
- `@PATCH` (no `@Path`) -> matches `.../{collection}` (collection root) = `updateMany`
- `@Path("update-bulk") @PATCH` -> matches `.../{collection}/update-bulk` = `updateBulk`
- `@Path("{doc-id}") @PATCH` -> matches `.../{collection}/{doc-id}` = `updateOne`

A `PATCH .../{collection}/update-bulk` could textually match both `update-bulk` and `{doc-id}`; the literal `update-bulk` wins, so no explicit disambiguation is needed. (Same reason `@Path("count")` coexists with `@Path("{doc-id}")` under different verbs.)

## Related

- [[luz_jsonstore V2 BSON endpoints must be Document-in Document-out]]
