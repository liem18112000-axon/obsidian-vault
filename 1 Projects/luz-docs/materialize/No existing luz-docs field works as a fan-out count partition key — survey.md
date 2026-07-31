---
title: "No existing luz-docs field works as a fan-out count partition key — survey"
created: 2026-06-16
type: argument
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [luz-docs, materialize, mongodb, partition, design]
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
