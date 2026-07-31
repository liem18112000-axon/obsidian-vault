---
title: "jsonstore $in vs $nin ObjectId conversion gap"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs parallelize code review, 2026-07-09/10"
tags: [mongodb, jsonstore, objectid, gotcha, code-review]
---

# jsonstore $in vs $nin ObjectId conversion gap

MongoDB queries via an HTTP microservice layer sometimes convert client-supplied ID strings to BSON ObjectId before querying — but that conversion middleware can special-case the operator by exact key string, and easily omit the negated form.

Concretely: in one codebase's jsonstore-style service, a `convertToObjectId` helper walks a filter document and converts string IDs to `ObjectId` only when it sees the literal key `"$in"`. It has no branch for `"$nin"` (exact `.equals()` check, and `"$in".equals("$nin")` is false). Since `_id` is stored as a real BSON `ObjectId` in Mongo (confirmed elsewhere: a client-supplied `_id` is stripped before insert, and the read-back path does an `instanceof ObjectId` check), a filter like `{_id: {$nin: ["abc123..."]}}` never gets its array elements converted — they stay BSON `String`s. A BSON `String` never equals a BSON `ObjectId`, so the $nin condition is satisfied by every document: the exclusion silently becomes a no-op that excludes nothing.

**Lesson:** when auditing an ID-conversion or type-coercion middleware layer, always check whether it handles the *negated* form of every operator it supports ($nin vs $in, $ne vs $eq, $nor vs $or). Negation operators look 'covered' at a glance (same field, similar shape) but are easy to leave out since they're written as a separate literal-string branch, not derived from the positive form. The failure mode is silent and structural — the query still runs successfully, it just matches the wrong set of documents.

Found while reviewing luz_docs' `ParallelizeMigrationExecutor`, which built an `_id: {$nin: excludeIds}` clause meant to skip permanently-failing documents on retry — the omission meant failed documents were retried forever instead of being skipped.
