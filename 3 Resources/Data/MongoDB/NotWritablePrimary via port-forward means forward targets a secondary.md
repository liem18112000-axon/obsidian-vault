---
ai_hash: 9d62ceba94b7c004
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22; canary tenant unset 2026-07-22
status: seedling
tags:
- mongodb
- kubernetes
- kubectl
- port-forward
- luz
- gotcha
title: NotWritablePrimary via port-forward means forward targets a secondary
type: lesson
---

# NotWritablePrimary via port-forward means forward targets a secondary

Writes over a `kubectl port-forward` to a Mongo pod failing with error **10107 `NotWritablePrimary`** ("not primary", errorLabels `RetryableWriteError`) mean the forward targets a **SECONDARY**. `localhost:27017` is whatever single pod you forwarded and the driver is pinned to it by `directConnection=true`, so it cannot fail over — and `readPreference=primary` in the URI changes nothing. Reads still succeed (stats scripts pass), which is why a *reused* "localhost:27017 already reachable" forward is the classic trap.

Fix — ask the replica set who is primary, then forward that pod on a fresh port:

```bash
kubectl exec <any-rs-pod> -n dev-mongodb-clusters -- mongo --quiet --eval "db.isMaster().primary"
# equivalently: db.command({hello:1}).primary  → primary's pod DNS name
kubectl port-forward <primary-pod> 27017:27017 -n dev-mongodb-clusters
```

The primary moves on failover — always re-probe, never hardcode a pod (as of 2026-07-22 luz-mongodb01 primary = rs-0). The `earchive-*` skills automate this with an insert+drop probe. Tenant-db auth convention on dev Luz mongo: `user = pass = authSource = db = tenantId`.

## Related

- [[directConnection=true counts read only the connected node and can be stale on a secondary]]
- [[Dev mongod pods have legacy mongo shell only, no mongosh]]
- [[3 Resources/Infra/Kubernetes/Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures]]

%% ai-graph-start %%

**Related notes:**
- [[Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures]]
- [[earchive-seed-stale-27017-portforward-gotcha]]
- [[Dev mongod pods have legacy mongo shell only, no mongosh]]
- [[Reuse an existing kubectl port-forward for ad-hoc mongo scripts]]
- [[directConnection=true counts read only the connected node and can be stale on a secondary]]

%% ai-graph-end %%