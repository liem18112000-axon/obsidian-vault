---
ai_hash: b1d0a5edb2ade1ef
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities:
- luz-docs field
- fan-out count partition key
- LUZ-154613
- materialised docs
- gateway
- NATIVE indexed range
- typed fields
- coercion
- $toObjectId
- $dateFromString
- $expr
- full scan
- K buckets
- _id
- ObjectId
- $in
- _createdDate
- _updatedDate
- BSON Date
- JsonStoreSearchQueryUtil.buildDateFromStringQuery
- _versionNumber
- int
- _isPublic
- bool
- _createdBy
- _updatedBy
- name
- _sizeInBytes
- _countShard
- SHARD_SPACE
- Partition the materialized count on a uniform _countShard int, not _id
- MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index
  (full scan)
- undercount
- requirements
- dedicated stored field
- balance
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- luz-docs
- materialize
- mongodb
- partition
- design
title: No existing luz-docs field works as a fan-out count partition key — survey
type: argument
---

# No existing luz-docs field works as a fan-out count partition key — survey

Survey of existing luz-docs document fields as a fan-out count partition key (LUZ-154613). Requirements: (1) present on 100% of materialised docs (else silent undercount), (2) plain scalar so the gateway does a NATIVE indexed range — typed fields force coercion ($toObjectId / $dateFromString) inside $expr which full-scans, (3) enough cardinality/spread to balance K buckets.

Result — NO existing field satisfies all three:
- _id: always present, but ObjectId → gateway coerces only for eq/$in; range needs $expr+$toObjectId → scan.
- _createdDate/_updatedDate: always present, high cardinality, but BSON Date → string bounds need $dateFromString (the date analogue of $toObjectId; see JsonStoreSearchQueryUtil.buildDateFromStringQuery) → $expr → scan. Also time-skewed.
- _versionNumber: plain int, always present, but ~all docs = 1 → degenerate (one bucket).
- _isPublic: bool (2 values); _createdBy/_updatedBy: few distinct users/tenant → no spread.
- name / _sizeInBytes: plain + good spread BUT not guaranteed on every doc → undercount.

Therefore a dedicated stored field is required for a correct+fast+balanced fan-out: _countShard = uniform int in [0, SHARD_SPACE), native indexed range, equal cuts balance with no quantile. This is the justification for adding a field rather than reusing one.

## Related

- [[1 Projects/luz-docs/materialize/Partition the materialized count on a uniform _countShard int, not _id]]
- [[MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan)]]

%% ai-graph-start %%

**Related notes:**
- [[Partition the materialized count on a uniform _countShard int, not _id]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[Random shard key gives balanced fan-out partitions (equal-width = equal-work only if uniform)]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]

**Relations:**
- luz-docs field — *is surveyed for* — fan-out count partition key
- LUZ-154613 — *is a* — survey
- fan-out count partition key — *has* — requirements
- requirements — *include* — present on 100% of materialised docs
- requirements — *include* — plain scalar
- requirements — *include* — enough cardinality/spread
- plain scalar — *enables* — NATIVE indexed range
- NATIVE indexed range — *is performed by* — gateway
- typed fields — *force* — coercion
- coercion — *occurs inside* — $expr
- coercion — *causes* — full scan
- $toObjectId — *is a type of* — coercion
- $dateFromString — *is a type of* — coercion
- $expr — *causes* — full scan
- enough cardinality/spread — *is needed for* — balance
- balance — *of* — K buckets
- _id — *is a* — luz-docs field
- _id — *has type* — ObjectId
- _id — *is* — always present
- _id — *requires* — $toObjectId
- $toObjectId — *for* — range queries
- $toObjectId — *with* — $expr
- $toObjectId — *on* — _id
- $toObjectId — *causes* — full scan
- _createdDate — *is a* — luz-docs field
- _updatedDate — *is a* — luz-docs field
- _createdDate — *has type* — BSON Date
- _updatedDate — *has type* — BSON Date
- _createdDate — *is* — always present
- _updatedDate — *is* — always present
- _createdDate — *has* — high cardinality
- _updatedDate — *has* — high cardinality
- BSON Date — *needs* — $dateFromString
- $dateFromString — *for* — string bounds
- $dateFromString — *is used in* — JsonStoreSearchQueryUtil.buildDateFromStringQuery
- $dateFromString — *with* — $expr
- $dateFromString — *causes* — full scan
- _createdDate — *is* — time-skewed
- _updatedDate — *is* — time-skewed
- _versionNumber — *is a* — luz-docs field
- _versionNumber — *has type* — int
- _versionNumber — *is* — always present
- _versionNumber — *is* — degenerate
- _isPublic — *is a* — luz-docs field
- _isPublic — *has type* — bool
- _isPublic — *has* — no spread
- _createdBy — *is a* — luz-docs field
- _updatedBy — *is a* — luz-docs field
- _createdBy — *has* — no spread
- _updatedBy — *has* — no spread
- name — *is a* — luz-docs field
- _sizeInBytes — *is a* — luz-docs field
- name — *is a* — plain scalar
- _sizeInBytes — *is a* — plain scalar
- name — *has* — good spread
- _sizeInBytes — *has* — good spread
- name — *is not* — guaranteed on every doc
- _sizeInBytes — *is not* — guaranteed on every doc
- not guaranteed on every doc — *causes* — undercount
- existing luz-docs field — *does not satisfy* — requirements
- dedicated stored field — *is* — required
- dedicated stored field — *for* — fan-out count partition key
- _countShard — *is a* — dedicated stored field
- _countShard — *is a* — uniform int
- _countShard — *is in range* — [0, SHARD_SPACE)
- _countShard — *enables* — NATIVE indexed range
- _countShard — *enables* — equal cuts balance
- Partition the materialized count on a uniform _countShard int, not _id — *is related to* — _countShard
- MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan) — *is related to* — $expr
- MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan) — *is related to* — $toObjectId
- MongoDB $expr + $toObjectId for _id range is correct but does not use the _id index (full scan) — *describes* — full scan

%% ai-graph-end %%