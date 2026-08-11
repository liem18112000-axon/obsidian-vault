---
ai_hash: 4fb8a7eebc2bc86c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-22
entities: []
source: sessions 2026-06-22, 2026-07-10
status: seedling
tags:
- luz-docs
- earchive
- seeding
- mongodb
- append
- kubectl
- recovery
title: Resume a partial earchive seed with APPEND instead of re-truncating
type: howto
---

# Resume a partial earchive seed with APPEND instead of re-truncating

To top up a partially-completed `earchive-data-prepare` run — whether a deliberate incremental add or recovery from a crash (`[prepare] fatal: read ECONNRESET` / `ECONNREFUSED` when the kubectl port-forward to the Mongo primary drops mid-generation) — re-invoke `prepare.sh` with the **reach-a-target** knobs instead of restarting from scratch:

```
APPEND=true FOLDER_COUNT=0 DOC_COUNT=<target - existing documents>
```

- `APPEND=true` skips the truncate.
- `FOLDER_COUNT=0` **reuses the existing folders** (loads them from Mongo) instead of building a new tree, so new docs attach to the same seed folders.
- `DOC_COUNT` is the **delta**, not the target.

The existing count must come from **Mongo, never the failed run's log** — spin a throwaway `kubectl port-forward` to the confirmed primary (named in the crashed run's own log line, e.g. `luz-mongodb00-cluster-rs-2`) and run `countDocuments()` on `folders` and `documents`.

**Auth gotcha for the count query:** the tenant Mongo uses `user = pass = authSource = tenantId`; an unauthenticated connect fails with `command count requires authentication`. Connection string:
`mongodb://<tenant>:<tenant>@localhost:<port>/<tenant>?authSource=<tenant>&directConnection=true`

**Which cluster:** first hex char of the tenant id mod K (dev K=4) → `luz-mongodbNN`. Tenant `d0783310…` → `d` % 4 = 1 → `luz-mongodb01`.

Safe to re-run because inserts are unordered and partial generation is explicitly recoverable.

## Related

- [[Recount before every reach-a-target retry — crashed runs insert silently]]
- [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]
- [[earchive-data-prepare logs document progress only every 10 batches]]
- [[earchive-prepare-knobs]]

%% ai-graph-start %%

**Related notes:**
- [[Recount before every reach-a-target retry — crashed runs insert silently]]
- [[earchive-data-prepare wrapper exits 0 even when the generator dies mid-run (verify the log footer)]]
- [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]
- [[earchive-seed-stale-27017-portforward-gotcha]]
- [[earchive-data-prepare logs document progress only every 10 batches]]

%% ai-graph-end %%