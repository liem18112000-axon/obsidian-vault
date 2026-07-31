---
ai_hash: 7c83e3ab0ea7bef7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
tags:
- luz
- earchive
- mongodb
- kubectl
- port-forward
- gotcha
- dev
title: earchive seed "Authentication failed" = stale localhost:27017 port-forward
type: resource
---

# earchive seed auth fail = stale 27017 port-forward

## Symptom
`earchive-data-prepare` (and siblings: `earchive-data-clean`, `earchive-materialize-index`, `luz-skill-materialize-stats`) probes every replica of the derived cluster and dies with:

```
[prepare] fatal: Authentication failed.
[prepare] no primary found across luz-mongodb0X-cluster-rs-{0,1,2}
```

even though tenant/cluster/creds are all correct.

## Root cause
A **leftover `kubectl port-forward` already listening on `localhost:27017`** (from a prior session, pointing at a *different* mongo cluster).

The skill does `kubectl port-forward pod 27017:27017 &` then a readiness check `echo > /dev/tcp/localhost/27017`. That check **connects to the stale listener** and passes, so the skill proceeds — but every query hits the wrong cluster, whose mongod rejects the tenant's `tenant:tenant` creds → "Authentication failed" on all 3 replicas.

## Why PORT override does NOT help
`prepare.sh` forwards `${PORT}:${PORT}` — local AND remote are the same value. The pod's mongod only listens on 27017, so setting `PORT=27019` forwards to a dead remote port. **Must free 27017**, not move off it.

## Fix
```bash
netstat -ano | grep LISTEN | grep :27017      # find PID holding it
powershell.exe -NoProfile -Command "Stop-Process -Id <PID> -Force"
netstat -ano | grep LISTEN | grep :27017      # confirm FREE
```
Then re-run the seed. Kill only after confirming it's a stray tunnel, not a real local mongod.

## Related
- Tenant `d0783310` lives on **cluster01** (`luz-mongodb01`); skill auto-derives it correctly now (first hex `d`=13, 13 mod 4 = 1).
- Matches the perf-env note "stale 27017 PF = false auth fail".
- Inspecting the holding process's *command line* via PowerShell gets blocked by the credential-exploration classifier — you don't need it; `netstat` + kill by PID is enough.

%% ai-graph-start %%

**Related notes:**
- [[NotWritablePrimary via port-forward means forward targets a secondary]]
- [[eArchive dev skills are self-contained copies, not shared helpers]]
- [[Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures]]
- [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]
- [[Reuse an existing kubectl port-forward for ad-hoc mongo scripts]]

%% ai-graph-end %%