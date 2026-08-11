---
ai_hash: 69b7f731b29f9330
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: session 2026-06-10, PagingBeanParam
status: seedling
tags:
- java
- jax-rs
- gotcha
- paging
- luz-docs
title: JAX-RS DefaultValue does not apply when a bean param is constructed in code
type: lesson
---

# JAX-RS DefaultValue does not apply when a bean param is constructed in code

A JAX-RS `@DefaultValue` annotation only kicks in when the container binds the field from an HTTP query/path param. Constructing the bean in Java (`new PagingBeanParam()`) leaves the field `null` — the annotation is ignored.

Gotcha seen in luz-docs: `getAllDocumentsMetadata(tenantId, bean, new PagingBeanParam(), ...)` ran with `size = null`, so the aggregation pipeline got no \ stage and fetched EVERY document of a folder into one JsonArray — fine on small tenants, blew up at 128k documents. The `@DefaultValue("0")` on `from` gave a false sense of safety.

Rule: never pass a no-args-constructed JAX-RS bean param into an internal query path expecting its annotated defaults; set paging explicitly (e.g. page with a do/while over `from`/`size`).

Related: [[luz-docs folder delete filter double-fetched every subfolder]]

## Related

- [[luz-docs folder delete filter double-fetched every subfolder]]

%% ai-graph-start %%

**Related notes:**
- [[Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple counts]]
- [[JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]
- [[Minimal meaningful test fixture size is bounded by the real page size]]
- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]
- [[luz-docs folder delete filter double-fetched every subfolder]]

%% ai-graph-end %%