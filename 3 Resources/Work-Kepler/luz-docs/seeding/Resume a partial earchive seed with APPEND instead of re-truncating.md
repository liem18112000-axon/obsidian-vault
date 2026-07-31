---
title: "Resume a partial earchive seed with APPEND instead of re-truncating"
created: 2026-06-22
type: howto
status: seedling
source: "session 2026-06-22"
tags: [luz-docs, seeding, mongodb, append]
---

# Resume a partial earchive seed with APPEND instead of re-truncating

To top up a partially-completed `earchive-data-prepare` seed run **without wiping** what already landed, run with `APPEND=true FOLDER_COUNT=0`: append mode skips truncate, and `FOLDER_COUNT=0` makes it **reuse the existing folders** (loads them from Mongo) instead of building a new folder tree, so new docs attach to the same seed folder.

Set `DOC_COUNT` to the *delta*, not the target: `DOC_COUNT = target - (actual current doc count)`. The actual count must come from Mongo, not the failed run log — read it with a `countDocuments()` on the tenant DB.

Gotcha for the count query: the tenant Mongo uses `user = pass = authSource = tenantId`; an unauthenticated connect fails with `command count requires authentication`. Connection string the skill uses:
`mongodb://<tenant>:<tenant>@localhost:<port>/<tenant>?authSource=<tenant>&directConnection=true`.

Which cluster: first hex char of the tenant id, mod K (dev K=4), picks `luz-mongodbNN`. Tenant `d0783310…` → `d`%4 = 1 → `luz-mongodb01`.

Related: [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]

## Related

- [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]
