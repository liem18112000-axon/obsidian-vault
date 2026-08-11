---
ai_hash: 845b4b5bc13d7a94
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- '_shard count: index bounds the slice but FETCHes every doc for un-indexed predicates'
created: 2026-06-17
entities:
- Production security count
- COUNT_SCAN
- benchmark query
- FETCH
- multikey
- $or
- $nin
- SECURITY query
- _effectiveSecurityClassCodes
- _isPublic
- $count aggregate plan
- idx_effectiveSecurityClassCodes_shard
- keys-only
- ZERO FETCH
- Fan-out
- keys-scan
- K parallel _shard slices
- benchmark's slowness
- query BODY
- index
- explain
- IXSCAN idx_shard
- nReturned
- keysExamined
- docsExamined
- eArchive list filter
- isStored
- folderIds
- letterInfo.mediaType
- Mongo
- BSON
- document read
- partialFilterExpression
- covered index
- partial index
- performance Ceiling
- pod CPU cores
- mongo-connection headroom
- infra
- luz-docs
- connectionPoolSize
- MicroProfile property
- exact, no-cache counts
- k8s
- covered compound index
- hot query
- predicate fields
- Roaring bitmap
- amplification
- popcount
- Method lesson
- find()
- visible-document count
- _shard fan-out
- idx_shard
- local port-forward
- speedup
source: LUZ-154613 session 2026-06-17, explain on dev (tenant 128k)
status: seedling
tags:
- luz-docs
- mongodb
- count
- covered-index
- explain
- fetch
- performance
title: Production security count is already COUNT_SCAN (covered); benchmark query's
  FETCH is inherent (multikey+$or+$nin)
type: observation
---

# Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)

**Production count is already optimal.** The real materialised count is the SECURITY query (`_effectiveSecurityClassCodes ∈ codes OR _isPublic`); its `$count` aggregate plan is `COUNT_SCAN,COUNT_SCAN` end-to-end on `idx_effectiveSecurityClassCodes_shard` — keys-only, ZERO FETCH. Fan-out just splits that keys-scan into K parallel `_shard` slices.

**The benchmark's slowness is its BODY, not the index.** explain of one K=16 `_shard` slice: winningPlan `IXSCAN idx_shard → FETCH`, `nReturned = keysExamined = docsExamined = 7993`, ~1.9s. The `_shard` range bounds candidates to exactly the slice (no over-scan), but the eArchive list filter (`$or(isStored, folderIds exists)` AND `$nin letterInfo.mediaType`) is not in the index, so Mongo FETCHes every candidate's full BSON to evaluate it. `docsExamined == keysExamined == nReturned` is the signature: perfect index bounds, still one document read per candidate. The FETCH — not the index scan — is the per-sub-count cost, which is exactly why fan-out yields ~1.8x (it divides FETCH-and-evaluate work by K).

That FETCH is **inherent**: `folderIds` is multikey plus `$or` plus `$nin`, and `partialFilterExpression` cannot express `$or`/`$nin`, so no covered or partial index rescues it.

**Ceiling** (~3.7s at K=16 vs ~1.9s single slice) is pod CPU cores / mongo-connection headroom — infra, not a luz-docs knob (no explicit rest-client pool config; `connectionPoolSize` isn't a standard MicroProfile property).

**Conclusion:** no luz-docs code change optimises this further for exact, no-cache counts. Remaining exact levers: more cores/connections (k8s), a covered compound index per hot query (predicate fields BEFORE `_shard` → keys-only COUNT_SCAN), or a Roaring bitmap (removes FETCH + amplification entirely, popcount with no per-doc read).

**Method lesson:** explain with `$count` (COUNT_SCAN) — never `find()`, which always FETCHes — to judge whether a count is covered.

## Related

- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]

%% ai-graph-start %%

**Related notes:**
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
- [[Divide-and-Conquer Visible-Document Count]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]

**Relations:**
- Production security count — *is* — optimal
- Production security count — *uses* — SECURITY query
- SECURITY query — *has* — $count aggregate plan
- $count aggregate plan — *is* — COUNT_SCAN,COUNT_SCAN
- $count aggregate plan — *on* — idx_effectiveSecurityClassCodes_shard
- idx_effectiveSecurityClassCodes_shard — *enables* — keys-only
- idx_effectiveSecurityClassCodes_shard — *results in* — ZERO FETCH
- SECURITY query — *involves* — _effectiveSecurityClassCodes
- SECURITY query — *involves* — _isPublic
- Fan-out — *splits* — keys-scan
- keys-scan — *into* — K parallel _shard slices
- benchmark query — *has* — benchmark's slowness
- benchmark's slowness — *is in* — query BODY
- benchmark's slowness — *is not in* — index
- explain — *shows* — IXSCAN idx_shard
- IXSCAN idx_shard — *leads to* — FETCH
- IXSCAN idx_shard — *has* — perfect index bounds
- IXSCAN idx_shard — *results in* — nReturned = keysExamined = docsExamined
- eArchive list filter — *is not in* — index
- eArchive list filter — *includes* — isStored
- eArchive list filter — *includes* — folderIds
- eArchive list filter — *includes* — letterInfo.mediaType
- Mongo — *FETCHes* — BSON
- FETCH — *is* — per-sub-count cost
- Fan-out — *divides* — FETCH-and-evaluate work
- FETCH — *is* — inherent
- inherent FETCH — *due to* — multikey
- inherent FETCH — *due to* — $or
- inherent FETCH — *due to* — $nin
- partialFilterExpression — *cannot express* — $or
- partialFilterExpression — *cannot express* — $nin
- covered index — *cannot rescue* — inherent FETCH
- partial index — *cannot rescue* — inherent FETCH
- performance Ceiling — *is* — pod CPU cores
- performance Ceiling — *is* — mongo-connection headroom
- performance Ceiling — *is* — infra
- luz-docs — *has no* — code change for optimisation
- optimisation lever — *is* — more cores
- optimisation lever — *is* — more connections
- more cores — *in* — k8s
- more connections — *in* — k8s
- optimisation lever — *is* — covered compound index
- covered compound index — *for* — hot query
- covered compound index — *includes* — predicate fields
- covered compound index — *enables* — keys-only COUNT_SCAN
- optimisation lever — *is* — Roaring bitmap
- Roaring bitmap — *removes* — FETCH
- Roaring bitmap — *removes* — amplification
- Roaring bitmap — *enables* — popcount
- Method lesson — *recommends* — explain with $count
- $count — *is* — COUNT_SCAN
- find() — *always* — FETCHes
- visible-document count — *can be optimised by* — _shard fan-out
- _shard fan-out — *uses* — idx_shard
- local port-forward — *masks* — speedup

%% ai-graph-end %%