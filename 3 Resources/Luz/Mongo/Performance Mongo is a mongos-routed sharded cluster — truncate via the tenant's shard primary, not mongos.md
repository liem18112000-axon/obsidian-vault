---
title: "Performance Mongo is a mongos-routed sharded cluster — truncate via the tenant's shard primary, not mongos"
created: 2026-08-13
type: gotcha
status: seedling
source: "luz-docs-import perf benchmark 2026-08-13"
tags: [luz, mongodb, sharding, mongos, performance-env, gotcha]
---

# Performance Mongo is a mongos-routed sharded cluster — truncate via the tenant's shard primary, not mongos

The Luz **performance** environment's Mongo is a **16-shard, mongos-routed sharded cluster** (`performance-mongodb-clusters`, replica sets `luz-mongodb00-cluster-rs` … `luz-mongodb0f-cluster-rs`, plus a `luz-mongodb-cluster-mongos` router). This differs from **dev**, which the `earchive-data-clean` skill assumes: 4 shards indexed by `first-hex-char-of-tenant mod 4`, connected directly.

Gotchas for cleaning/accessing a tenant's data on performance:
- **A tenant's DB lives on the shard matching the first hex char of its id** (e.g. tenant `45b0…` → shard **`luz-mongodb04-cluster-rs`**), not `mod 4`, and not spread across shards (databases aren't sharded, only collections would be).
- **Per-tenant auth (`user=pass=authSource=db=tenantId`) works only on a DIRECT connection to that shard's replica set** — connecting through the **mongos router rejects it** (`Authentication failed`). So truncate by port-forwarding the shard **primary** pod directly (`directConnection=true`), not mongos.
- **Writes (`deleteMany`) require the replica-set PRIMARY** — probe/insert to confirm which pod is primary before deleting (a secondary rejects writes).

So `earchive-data-clean`'s dev auto-discovery does not work on performance; point `clean_data.js` at the tenant's shard-primary port-forward instead (its `PORT`/`TENANT_ID`/`CONFIRM` params are reusable as-is).

Related: [[luz-docs-import performance-env import benchmark findings]].

## Related

- [[luz-docs-import performance-env import benchmark findings]]
