---
title: "NotWritablePrimary via port-forward means forward targets a secondary"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22"
tags: [mongodb, kubernetes, port-forward, gotcha]
---

# NotWritablePrimary via port-forward means forward targets a secondary

Writes over a `kubectl port-forward` to a Mongo pod failing with error 10107 `NotWritablePrimary` ("not primary", errorLabels RetryableWriteError) mean the forward targets a SECONDARY replica — localhost:27017 is whatever single pod you forwarded, and the driver cannot fail over through it.

Fix: ask the replica set who is primary, then forward that pod:

```bash
kubectl exec <any-rs-pod> -n dev-mongodb-clusters -- mongo --quiet --eval "db.isMaster().primary"
kubectl port-forward <primary-pod> 27017:27017 -n dev-mongodb-clusters
```

As of 2026-07-22, luz-mongodb01 primary = rs-0 (rs-1 secondary) — but the primary moves on failover, so always re-probe rather than hardcoding a pod. The earchive-* skills automate this discovery with an insert+drop probe.

## Related

- [[Dev mongod pods have legacy mongo shell only]]
- [[no mongosh]]
- [[Stale kubectl port-forward silently holds local port on Windows]]
