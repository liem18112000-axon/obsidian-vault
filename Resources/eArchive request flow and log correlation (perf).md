---
title: eArchive request flow and log correlation (perf)
tags: [luz, earchive, logs, performance, latency, parallelize]
created: 2026-07-16
---

# eArchive request flow and log correlation (perf)

## Tenant correlation key
The eArchive page is **JSF/PrimeFaces server-side** (webclient → view-controller → luz-docs → jsonstore); the browser never calls luz-docs directly. But the **same tenant UUID flows through every service** and appears in `luz-uri=` access logs. For LiemCompany on performance the tenant = `45b05710-b9d4-4d3e-935e-83c4525369fa` (found via GA `up.tenant=` in browser network, confirmed in view-controller + luz-docs access logs). First hex `4` → mongo cluster `luz-mongodb04`.

So `luz-skill-flow-logs 45b05710-... SEVERITY= FRESHNESS=<win>` (no severity filter) isolates one session's whole cross-service trace. Gotcha: a too-short sample window or low `--limit` on desc order can return only the trailing file-fetch calls and miss the load burst → looks like 0 hits. Widen freshness/limit.

## Endpoints fired by one eArchive load (all under `/api/<tenant>/`)
- view-controller `GET  /letters/badge-count` — the header badge counts
- view-controller `POST /letters/count` (several — per directory/filter combo)
- view-controller `POST /documents/search` — the doc **list** page → downstream `luz-docs:8080/luz_docs/api/<t>/documents/search`
- view-controller `GET  /v2/<t>/archives/directories/branded` — folder widget
- luz-docs `POST /documents/count?skip-security-classes=...&include-folder-names=...` — the **parallelize K fan-out** count (K=`LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS`)
- per-doc `GET /documents/{oid}/files/thumbnail128` (many; some 404) and `.../files/reference`

## Latency signal
`io.undertow.accesslog` lines end with `time-consuming=<ms>`. Observed baseline (K=12, first load): `documents/search` **≈ 143 800 ms** — the dominant cost, not count. Count is **cached** (~60s/3600s) so repeat loads may skip the K fan-out entirely — to actually exercise K, bust/wait out the count cache between trials.

## Request shapes (from access logs + DocumentResource query-payload logging)
- **letters/badge-count** (GET, `languageCode/language=en&skip-security-classes=false&is-business-tenant=true`) internally fans a separate luz-docs `documents/count` per badge → its 872 s.
- **letters/search** (POST v2, list page): `value=` empty ⇒ list all, `from/size=0/48` paging, `sortField=_updatedDate&sortMode=DESC`, 12 `excludes[]` history fields, `exclude-total-count=true` (fast).
- **letters/count** (POST v2): one call **per folder** via `directoryId={oid}` — the serial fan-out.
- **luz-docs documents/count** body = the unread-badge query: `{"query":{"and":[{"or":[ readHistoryEntries-not-exists + _createdDate>=2021-11-01 , markedAsUnread=true ]}, { folderIds-not-exists + isStored=false + storedFolder-not-exists + origin≠"User uploaded" }, { letterInfo.mediaType not in [smartletter.draft…] }]}}`. luz-docs splits it into K=12 sub-counts on the `_shard` field and sums.
- **jsonstore documents/count** = same query forwarded **per partition** with an added `_shard` range predicate (one Mongo countDocuments/shard, ~300 ms). 180 calls = 12 partitions × the badge/count queries.
- **jsonstore documents/aggregate** carries `collation="locale":"en","caseFirst":"UPPER"` (en/UPPER-aware sort for folder-name grouping).

Related: [[eArchive page DOM selectors (performance automation)]] · [[Luz K count-partitions env var]] · [[Luz performance env cluster topology]] · [[eArchive 800k bottleneck is view-controller not K]]
