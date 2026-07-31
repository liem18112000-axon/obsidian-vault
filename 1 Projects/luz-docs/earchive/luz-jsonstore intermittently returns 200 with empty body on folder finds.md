---
title: "luz-jsonstore intermittently returns 200 with empty body on folder finds"
created: 2026-06-11
type: observation
status: seedling
source: "dev flow logs 2026-06-11 ~11:58 CEST"
tags: [luz-jsonstore, luz-docs, earchive, migration, bug]
---

# luz-jsonstore intermittently returns 200 with empty body on folder finds

During eArchive materialise migration on dev, luz-jsonstore intermittently (~1.7%% of folder-find POSTs, 3 of ~180 in a 3h window) returns **HTTP 200 with `content-length: 0`** instead of the result array. luz-docs `MaterializeMigrationExecutor.getFolders` then throws `JsonParsingException: Invalid token=EOF` (see [[JsonParsingException EOF offset -1 means an empty response body was parsed]]).

Diagnostic fingerprint: ClientResponseFilter on luz-docs shows `status-code=200, content-length=0, content-type=application/json, x-envoy-upstream-service-time=<n>` — upstream answered, so it is jsonstore (or its response serialization), not envoy, producing the empty entity. jsonstore-side logs for the same request look fully normal (`query 1`, accesslog 200, a few ms) and its accesslog always prints `bytes-sent=-`, so it cannot confirm body size.

Mitigations: retry-on-empty-body in the executor (cheap, immediate); root-cause jsonstore getMany entity-writing path for a race that emits 200 with null/empty entity.

## Related

- [[JsonParsingException EOF offset -1 means an empty response body was parsed]]

> [!warning] Superseded
> Root cause found: NOT a flake. jsonstore returns 200 + empty string **by design** on zero matches; the failing docs carry orphaned folderIds, so their folder find matches nothing — deterministic per doc. See [[luz-jsonstore find returns 200 empty string, not [], on zero matches]].
