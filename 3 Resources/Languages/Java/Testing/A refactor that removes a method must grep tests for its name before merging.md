---
ai_hash: 14f27c8c86073f8e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: 'session 2026-07-21, luz-docs PR #1363'
status: seedling
tags:
- java
- maven
- refactoring
- gotcha
- luz-docs
title: A refactor that removes a method must grep tests for its name before merging
type: lesson
---

# A refactor that removes a method must grep tests for its name before merging

A merge can leave the default branch unable to compile its tests when a refactor deletes a method but keeps tests that call it. In luz-docs, merge PR #1363 removed `isCompletedCheckL2` from MaterializeGate/ParallelizeGate while both gate tests still asserted against it — `mvn test-compile` was broken at HEAD until the next feature branch fixed it.

**Lesson:** when removing any method (especially package-private ones invisible to callers outside the package), grep the *test* tree for the method name before merging — IDE-driven refactors and manual mirror-edits across twin classes both miss stale test references. CI that skips test compilation will not catch it.

## Related

- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]

%% ai-graph-start %%

**Related notes:**
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]
- [[Run the full affected test package locally, not a hand-picked subset]]
- [[Verify test files still exist on disk before trusting prior green test runs]]
- [[A merged-in test breaks when the target branch's service gained a new injected dependency]]

%% ai-graph-end %%