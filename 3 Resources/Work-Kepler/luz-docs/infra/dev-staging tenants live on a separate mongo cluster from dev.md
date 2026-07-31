---
ai_hash: 9b49647feda0ef19
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: session 2026-06-17
status: seedling
tags:
- luz-docs
- mongodb
- dev-staging
- gotcha
title: dev-staging tenants live on a separate mongo cluster from dev
type: lesson
---

# dev-staging tenants live on a separate mongo cluster from dev

dev-staging tenants do NOT live on the default dev mongo cluster. Tenant `6873c725-bf58-4c9e-b9bf-173059b692cd` (and `9f66283e-4bfc-4e18-a992-abbdbe883725`) authenticate against **namespace `dev-staging-mongodb-clusters`**, pod **`luz-mongodb01-cluster-rs-0`** — not `dev-mongodb-clusters / luz-mongodb02-cluster-rs-0` (the default the materialize-stats skill assumes).

Symptom when you target the wrong cluster: `MongoServerError: Authentication failed (code 18)` — because the tenant DB/user only exists on its own cluster. dev-staging has two clusters (`luz-mongodb00-cluster`, `luz-mongodb01-cluster`); a tenant lives on one, so try each rs-0 if auth fails.

How to run materialize-stats against dev-staging:
`MONGO_NAMESPACE=dev-staging-mongodb-clusters MONGO_POD=luz-mongodb01-cluster-rs-0 check_materialize.sh <TENANT_ID>`

Mongo namespaces seen in cluster: dev-mongodb-clusters, dev-staging-mongodb-clusters, test-mongodb-clusters, swissdec-mongodb-clusters (+ legacy *-mongodb01/02).

Related: [[dev-staging luz-docs IT failures cluster on the materialize read-path]].

## Related

- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]

%% ai-graph-start %%

**Related notes:**
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]
- [[eArchive dev skills are self-contained copies, not shared helpers]]
- [[earchive-seed-stale-27017-portforward-gotcha]]
- [[Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char]]

%% ai-graph-end %%