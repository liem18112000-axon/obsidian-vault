---
ai_hash: 9608bb481ec86c25
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22
status: seedling
tags:
- mongodb
- kubernetes
- luz
- gotcha
title: Dev mongod pods have legacy mongo shell only, no mongosh
type: lesson
---

# Dev mongod pods have legacy mongo shell only, no mongosh

The mongod pods in `dev-mongodb-clusters` (GKE, klara-nonprod) do NOT have `mongosh` in `$PATH` — only the legacy `mongo` shell. Any `kubectl exec ... mongosh` fails with `executable file not found in $PATH`.

Probe replica-set state with the legacy shell instead:

```bash
kubectl exec luz-mongodb01-cluster-rs-1 -n dev-mongodb-clusters -- mongo --quiet --eval "db.isMaster().primary"
```

`db.isMaster()` works unauthenticated, so this is the cheapest way to find the primary from outside.

## Related

- [[NotWritablePrimary via port-forward means forward targets a secondary]]

%% ai-graph-start %%

**Related notes:**
- [[NotWritablePrimary via port-forward means forward targets a secondary]]
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]
- [[Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures]]
- [[dev-staging tenants live on a separate mongo cluster from dev]]
- [[eArchive dev skills are self-contained copies, not shared helpers]]

%% ai-graph-end %%