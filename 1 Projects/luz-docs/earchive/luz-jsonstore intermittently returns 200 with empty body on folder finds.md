---
ai_hash: 76826ee759dde26c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz-jsonstore
- HTTP 200 with empty body
- eArchive materialise migration
- luz-docs
- MaterializeMigrationExecutor
- 'JsonParsingException: Invalid token=EOF'
- result array
- ClientResponseFilter
- envoy
- jsonstore accesslog
- retry-on-empty-body
- getMany entity-writing path
- JsonParsingException EOF offset -1 means an empty response body was parsed
- luz-jsonstore find returns 200 empty string, not [], on zero matches
- orphaned folderIds
- zero matches
source: dev flow logs 2026-06-11 ~11:58 CEST
status: seedling
tags:
- luz-jsonstore
- luz-docs
- earchive
- migration
- bug
title: luz-jsonstore intermittently returns 200 with empty body on folder finds
type: observation
---

# luz-jsonstore intermittently returns 200 with empty body on folder finds

During eArchive materialise migration on dev, luz-jsonstore intermittently (~1.7%% of folder-find POSTs, 3 of ~180 in a 3h window) returns **HTTP 200 with `content-length: 0`** instead of the result array. luz-docs `MaterializeMigrationExecutor.getFolders` then throws `JsonParsingException: Invalid token=EOF` (see [[JsonParsingException EOF offset -1 means an empty response body was parsed]]).

Diagnostic fingerprint: ClientResponseFilter on luz-docs shows `status-code=200, content-length=0, content-type=application/json, x-envoy-upstream-service-time=<n>` — upstream answered, so it is jsonstore (or its response serialization), not envoy, producing the empty entity. jsonstore-side logs for the same request look fully normal (`query 1`, accesslog 200, a few ms) and its accesslog always prints `bytes-sent=-`, so it cannot confirm body size.

Mitigations: retry-on-empty-body in the executor (cheap, immediate); root-cause jsonstore getMany entity-writing path for a race that emits 200 with null/empty entity.

## Related

- [[JsonParsingException EOF offset -1 means an empty response body was parsed]]

> [!warning] Superseded
> Root cause found: NOT a flake. jsonstore returns 200 + empty string **by design** on zero matches; the failing docs carry orphaned folderIds, so their folder find matches nothing — deterministic per doc. See [[luz-jsonstore find returns 200 empty string, not [], on zero matches]].

%% ai-graph-start %%

**Related notes:**
- [[luz-jsonstore find returns 200 empty string, not [], on zero matches]]
- [[JsonParsingException EOF offset -1 means an empty response body was parsed]]
- [[luz-docs getDocumentById returns empty object not null for missing docs]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]

**Relations:**
- luz-jsonstore — *intermittently returns* — HTTP 200 with empty body
- luz-jsonstore — *should return* — result array
- eArchive materialise migration — *involves* — luz-jsonstore
- MaterializeMigrationExecutor — *is part of* — luz-docs
- MaterializeMigrationExecutor — *calls* — getFolders
- luz-docs — *throws* — JsonParsingException: Invalid token=EOF
- JsonParsingException: Invalid token=EOF — *is caused by* — HTTP 200 with empty body
- JsonParsingException: Invalid token=EOF — *references* — JsonParsingException EOF offset -1 means an empty response body was parsed
- ClientResponseFilter — *on* — luz-docs
- ClientResponseFilter — *shows* — HTTP 200 with empty body
- envoy — *is not responsible for* — HTTP 200 with empty body
- luz-jsonstore — *is responsible for* — HTTP 200 with empty body
- jsonstore accesslog — *cannot confirm* — body size
- retry-on-empty-body — *is a mitigation for* — HTTP 200 with empty body
- getMany entity-writing path — *was a suspected root-cause for* — HTTP 200 with empty body
- luz-jsonstore — *returns* — HTTP 200 with empty body
- HTTP 200 with empty body — *occurs on* — zero matches
- luz-jsonstore — *returns by design* — HTTP 200 with empty body
- failing docs — *contain* — orphaned folderIds
- orphaned folderIds — *lead to* — zero matches
- luz-jsonstore find returns 200 empty string, not [], on zero matches — *explains* — luz-jsonstore

%% ai-graph-end %%