---
ai_hash: 90b90fb9ff57d7e0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14 folderIds-facet investigation
status: seedling
tags:
- mongodb
- aggregation
- performance
- debugging
title: Mongo $group is blocking so time-to-error is scan-bound, not timeout-bound
type: lesson
---

# Mongo $group is blocking so time-to-error is scan-bound, not timeout-bound

MongoDB `$group` is a **blocking** aggregation stage: it must consume every input document before it can emit — or, in the failure case, before it can even detect it has exceeded the 100MB memory ceiling. Two consequences when debugging a slow-then-failing aggregation:

1. **Wall-clock to the error is scan-bound, not timeout-bound.** If the facet/group field is unindexed, the preceding `$match` is a full COLLSCAN, and *that* scan time dominates the ~20s+ before the error — the duration reflects data volume, not a configured timeout.
2. **Distinguish a real Mongo memory abort from a gateway/read timeout** by two signals: (a) the error carries Mongo`\s own code **292 `QueryExceededMemoryLimitNoDiskUseAllowed`** and codeName, and (b) failures recur *once per load-test iteration* at varying wall-clock, not clustered at a fixed ~20-30s boundary. A gateway/read timeout instead fails at a constant threshold with a generic timeout error.

Applied in [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]] to rule out a read-timeout explanation.

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
- [[luz-docs facet $unwind branch keys off client-supplied typearray, not schema]]
- [[Canary tenant eArchive folder list trips Mongo code 292 sort-memory-limit]]
- [[Mongo facet $group count index only helps the $match prefix, not the count]]
- [[Performance-env mongo cluster for a tenant = luz-mongodbNN by first hex char]]

%% ai-graph-end %%