---
title: "Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit"
created: 2026-07-31
type: lesson
status: seedling
source: "session 2026-07-31"
tags: [luz-docs, luz-jsonstore, mongodb, gotcha, earchive]
---

# Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit

Opening a populated eArchive folder for the canary tenant `d0783310-d67f-4ab7-9aab-dcaef3f17f48` (LiemCompany profile, seeded with a large earchive-data-prepare dataset on luz-mongodb01) throws a Mongo **error code 292** in **luz-jsonstore** `JsonStoreMongoDbResource.getMany`:

> Query failed ... 'Sort exceeded memory limit of 104857600 bytes, but did not opt in to external sorting. Aborting operation. Pass allowDiskUse:true'

The document-list query sorts the full folder result set in memory; past ~100 MB Mongo aborts unless `allowDiskUse:true` (or a sort-covering index) is set. It surfaces only on tenants with big datasets, so small tenants (e.g. 1.Miracle Company, 260 docs) never hit it.

Key attribution point: this is in **luz-jsonstore getMany (the find+sort list path)** — NOT in luz-docs and NOT in the materialize/parallelize gate/count/campaign-check code. So it is unrelated to gate refactors; a clean-refactor smoke test on this tenant will still show this ERROR in flow-logs. Fix belongs in the jsonstore find call (allowDiskUse) or an indexed sort, not the gates.

Related: [[Tenant d0783310 on cluster01]] (canary lives on luz-mongodb01).
