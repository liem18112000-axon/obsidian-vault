---
title: "Shape-keyed test mocks break when production query shapes change"
created: 2026-06-10
type: lesson
status: seedling
source: "luz-docs enhance-delete-folder-api, 2026-06-10"
tags: [testing, mocks, mongodb, luz-docs]
---

# Shape-keyed test mocks break when production query shapes change

A fixture mock that dispatches on the *shape* of a query filter (e.g. `filter.getString("folderIds")`) silently couples tests to the production code's query style. When the production path starts emitting new shapes — an exact array match `{folderIds: [id]}` or a positional-exists predicate `{"folderIds.1": {$exists: true}}` — the mock throws ClassCastException or matches the wrong fixtures.

When optimizing query patterns, audit the mocks for every new shape and make them honor the *semantics*, not just the key: in Mongo, `{arr: "x"}` is contains, `{arr: ["x"]}` is exact equality, and `{"arr.1": {$exists: true}}` means size ≥ 2. The mock should route each fixture by whether its actual array satisfies the incoming shape.

Related: [[Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]

## Related

- [[Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]
