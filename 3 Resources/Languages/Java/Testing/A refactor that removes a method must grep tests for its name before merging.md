---
title: "A refactor that removes a method must grep tests for its name before merging"
created: 2026-07-21
type: lesson
status: seedling
source: "session 2026-07-21, luz-docs PR #1363"
tags: [java, maven, refactoring, gotcha, luz-docs]
---

# A refactor that removes a method must grep tests for its name before merging

A merge can leave the default branch unable to compile its tests when a refactor deletes a method but keeps tests that call it. In luz-docs, merge PR #1363 removed `isCompletedCheckL2` from MaterializeGate/ParallelizeGate while both gate tests still asserted against it — `mvn test-compile` was broken at HEAD until the next feature branch fixed it.

**Lesson:** when removing any method (especially package-private ones invisible to callers outside the package), grep the *test* tree for the method name before merging — IDE-driven refactors and manual mirror-edits across twin classes both miss stale test references. CI that skips test compilation will not catch it.

## Related

- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]
