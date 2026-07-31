---
title: "luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)"
created: 2026-07-14
type: lesson
status: seedling
source: "session 2026-07-14 folderIds-facet investigation"
tags: [luz-docs, mongodb, gotcha, facet, earchive]
---

# luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)

The load-test row `documents/search · facet folderIds · 0/11 · 23.34(500)` is a genuine **server-side MongoDB abort**, not a gateway read-timeout that happens to land near 23s. The facet aggregation`\s `$group` stage exceeds Mongo`\s 100MB in-memory limit and returns error **292 `QueryExceededMemoryLimitNoDiskUseAllowed`**.

Root cause: `luz_jsonstore` never passes `allowDiskUse:true`. The generic aggregate call site is `JsonStoreMongoDbService.java:629` (`collection.aggregate(filters)`), and grepping the whole `luz_jsonstore` repo for `allowDiskUse` returns **zero** matches — so no caller of that endpoint can spill to disk. `luz-docs` `JsonStoreMongoService.getFacets` catches the failure and rethrows `DocumentException(CAN_NOT_GET_FACETS)`, surfaced to the client as **HTTP 500**.

Confirmed via `luz-skill-flow-logs` on the Performance env for tenant `45b05710-b9d4-4d3e-935e-83c4525369fa`: recurring `SEVERE ... aggregate: MongoCommandException ... error 292 ... on server luz-mongodb04-cluster-rs-2`, once per test iteration.

Top fix: set `allowDiskUse(true)` (ideally behind an explicit flag). Companion fixes: index `folderIds`, and ensure the facet request sends `"type":"array"` — see [[luz-docs facet $unwind branch keys off client-supplied type:array, not schema]].

## Related

- [[luz-docs facet $unwind branch keys off client-supplied type:array]]
- [[not schema]]
- [[Mongo $group is blocking so time-to-error is scan-bound]]
- [[not timeout-bound]]
