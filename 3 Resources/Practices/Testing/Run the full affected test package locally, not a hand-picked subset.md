---
ai_hash: 305095f3fe565790
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: luz_docs LUZ-154613 2026-06-17
status: seedling
tags:
- testing
- ci
- java
- gotcha
title: Run the full affected test package locally, not a hand-picked subset
type: lesson
---

# Run the full affected test package locally, not a hand-picked subset

Running a hand-picked SUBSET of tests locally can pass while the full CI build fails — sibling tests in the same package often assert the SAME behavior you changed.

Concrete: adding a `/_shard` JSON-Patch op to `MaterializeState.appendAsPatchOps` changed op-count assertions in BOTH `MaterializeStateTest` AND `MaterializeCascadeServiceTest`. I ran only `Parallelize*Test,MaterializeStateTest,MaterializeComputeTest,MaterializeRepositoryTest` (green), pushed, and the full CI `mvn` build then failed on 3 `MaterializeCascadeServiceTest` patch-op-count assertions (`patch.size()+4` -> `+5`).

Lesson: when a change alters shared output (a record field, a JSON shape, an op list, a count), run the WHOLE affected package (`-Dtest=ch.klara.luz.docs.materialize.**`), not a curated subset — or just run the full suite before pushing. Grep for every test asserting the old shape/size, too.

%% ai-graph-start %%

**Related notes:**
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]
- [[A refactor that removes a method must grep tests for its name before merging]]
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]

%% ai-graph-end %%