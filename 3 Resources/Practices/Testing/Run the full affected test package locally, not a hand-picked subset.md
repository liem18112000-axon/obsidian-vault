---
title: "Run the full affected test package locally, not a hand-picked subset"
created: 2026-06-17
type: lesson
status: seedling
source: "luz_docs LUZ-154613 2026-06-17"
tags: [testing, ci, java, gotcha]
---

# Run the full affected test package locally, not a hand-picked subset

Running a hand-picked SUBSET of tests locally can pass while the full CI build fails — sibling tests in the same package often assert the SAME behavior you changed.

Concrete: adding a `/_shard` JSON-Patch op to `MaterializeState.appendAsPatchOps` changed op-count assertions in BOTH `MaterializeStateTest` AND `MaterializeCascadeServiceTest`. I ran only `Parallelize*Test,MaterializeStateTest,MaterializeComputeTest,MaterializeRepositoryTest` (green), pushed, and the full CI `mvn` build then failed on 3 `MaterializeCascadeServiceTest` patch-op-count assertions (`patch.size()+4` -> `+5`).

Lesson: when a change alters shared output (a record field, a JSON shape, an op list, a count), run the WHOLE affected package (`-Dtest=ch.klara.luz.docs.materialize.**`), not a curated subset — or just run the full suite before pushing. Grep for every test asserting the old shape/size, too.
