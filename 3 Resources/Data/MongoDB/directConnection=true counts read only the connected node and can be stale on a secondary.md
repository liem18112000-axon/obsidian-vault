---
ai_hash: 7cb75ba956b924c1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities: []
source: session 2026-06-23 eArchive seed
status: seedling
tags:
- mongodb
- replica-set
- gotcha
- kubectl
- earchive
title: directConnection=true counts read only the connected node and can be stale
  on a secondary
type: lesson
---

# directConnection=true counts read only the connected node and can be stale on a secondary

A MongoDB connection string with `directConnection=true` makes the driver talk to **only the node it connected to** and **ignore `readPreference`** (even `primaryPreferred`). If that node is a lagging secondary, `countDocuments` returns a **stale, falsely-low** number, and any write/delete hard-fails because secondaries reject writes.

This is the standard shape when reaching a replica-set member through a single `kubectl port-forward`: you forward one pod (e.g. `luz-mongodb02-cluster-rs-0`) and must use `directConnection=true` because the driver cannot reach the other members by their internal cluster hostnames to do normal topology discovery.

**Gotcha that bit me (eArchive seed, 2026-06-23):** read 297,007 docs from `rs-0` after a re-election had made it a *secondary*, while the real primary already had 300,000. Trusting that count, I "topped up" 2,993 docs and overshot to 302,993.

**Fix / habit:** before trusting a `directConnection` count — or before any write — verify the node is primary with the `hello` command and check `isWritablePrimary` (the response also carries `primary` = the current primary host). If it is not primary, re-point the port-forward at the primary pod.

Relates to [[MongoDB ObjectId timestamp identifies recently-inserted docs]].

## Related

- [[MongoDB ObjectId timestamp identifies recently-inserted docs]]

%% ai-graph-start %%

**Related notes:**
- [[NotWritablePrimary via port-forward means forward targets a secondary]]
- [[MongoDB ObjectId timestamp identifies recently-inserted docs]]
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]
- [[Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures]]
- [[earchive-data-prepare wrapper exits 0 even when the generator dies mid-run (verify the log footer)]]

%% ai-graph-end %%