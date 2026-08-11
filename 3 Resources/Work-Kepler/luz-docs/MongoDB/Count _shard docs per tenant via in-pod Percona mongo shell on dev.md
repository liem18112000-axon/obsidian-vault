---
ai_hash: 48cdb239c51a7cc0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-15
status: seedling
tags:
- mongodb
- luz-docs
- kubectl
- percona
- sharding
title: Count _shard docs per tenant via in-pod Percona mongo shell on dev
type: howto
---

# Count _shard docs per tenant via in-pod Percona mongo shell on dev

To count documents by `_shard` presence for a Luz tenant on dev, exec into the Percona mongod pod and run a count with the operator admin credentials the container already exposes as env vars:

```bash
kubectl -n dev-mongodb-clusters exec <rs-pod> -c mongod -- bash -c '
DB="<tenant-id>"
mongo --quiet -u "$MONGODB_DATABASE_ADMIN_USER" -p "$MONGODB_DATABASE_ADMIN_PASSWORD" --authenticationDatabase admin \
  --eval "db=db.getSiblingDB(\"$DB\"); print(db.documents.countDocuments({_shard:{\$exists:true}}));"
'
```

Gotchas:
- Pods run legacy `mongo`, not `mongosh` (mongosh: command not found).
- `countDocuments` runs an aggregate, which fails on a secondary. Find the primary first with `db.hello().primary`, then run the count on that pod.
- Data-read cred env vars in-pod: MONGODB_DATABASE_ADMIN_USER / MONGODB_DATABASE_ADMIN_PASSWORD; rs-admin: MONGODB_CLUSTER_ADMIN_*.
- Tenant DB name == tenant id; documents live in the documents collection.

Cluster routing confirmed on dev (k=4, 0-indexed): first hex char of tenant id mod 4 -> luz-mongodb0{0..3}-cluster-rs. Tenant c1085cf9 -> hex c = 12, 12 mod 4 = 0 -> luz-mongodb00.

Observed 2026-07-15: tenant c1085cf9 had 6 / 200006 docs with _shard — PARALLEL_DOCUMENT_COUNT_SHARDING aborted early on a transient jwt-service connection-refused.

## Related

- [[luz-docs]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[dev-staging tenants live on a separate mongo cluster from dev]]
- [[Dev benchmark _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain]]
- [[Luz performance env cluster topology]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]

%% ai-graph-end %%