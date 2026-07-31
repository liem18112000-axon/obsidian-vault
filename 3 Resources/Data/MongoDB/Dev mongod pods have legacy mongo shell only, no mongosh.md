---
title: "Dev mongod pods have legacy mongo shell only, no mongosh"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22"
tags: [mongodb, kubernetes, luz, gotcha]
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
