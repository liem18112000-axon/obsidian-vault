---
ai_hash: a7b9eaef4bbb6522
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-13
entities: []
source: eArchive ops session 2026-07-13, tenant 45b05710 performance env
status: seedling
tags:
- mongodb
- luz-docs
- earchive
title: Luz eArchive tenant mongo database collection list
type: observation
---

# Luz eArchive tenant mongo database collection list

A Luz eArchive tenant's per-tenant mongo database has (at least) four collections: `documents`, `folders`, `profile`, and `luz_docs_migration_campaign`.

In a freshly-seeded tenant, only `documents` carries secondary indexes ([[eArchive documents collection has 7 materialise-related indexes, not 4]]) — `folders`, `profile`, and `luz_docs_migration_campaign` each had only the default `_id_` index. Useful baseline when scripting index audits/drops across a tenant DB: iterating `db.listCollections()` and checking each collection's index list is the safe general approach rather than hardcoding just `documents`+`folders`.

**Update 2026-07-13 (dev canary tenant d0783310-d67f-4ab7-9aab-dcaef3f17f48):** a real-app-touched tenant has a much longer collection list than the 4 above: `expirabledocuments`, `materializeCascade`, `groups`, `enrichmentstatus`, `materializeCascadeSnapshot` also exist, and in this case `folders` itself carried 3 secondary indexes (`idx_folders_parentFolderIds_updatedDate`, `idx_folders_securityClassCodes`, `idx_folders_inheritedSecurityClassCodes`) — so "only documents has indexes" only holds for skill-seeded-only tenants, not ones exercised by the real app. Also observed 3 stray probe collections (`_materialize_filter_query_probe`, `_materializeCascade`, `_materialize_filter_probe`) left behind by some other tool's unfinished cleanup — not from earchive-data-prepare (whose own probe is named `_earchive_data_prepare_probe`); harmless but worth knowing they can accumulate.

## Related

- [[eArchive documents collection has 7 materialise-related indexes, not 4]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive documents collection has 7 materialise-related indexes, not 4]]
- [[Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char]]
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]
- [[Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit]]
- [[eArchive dev skills are self-contained copies, not shared helpers]]

%% ai-graph-end %%