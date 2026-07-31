---
title: "Reshaping a class in a PR must update its tests in the same PR"
created: 2026-07-21
type: lesson
status: seedling
source: "session 2026-07-21 master build 72bfc3d7 failure"
tags: [testing, ci, luz-docs, gotcha]
---

# Reshaping a class in a PR must update its tests in the same PR

PR #1363 (luz_docs LUZ-156856) merged a gate reshape that inlined `isCompletedCheckL1/L2` into a checks-list inside `isShardingComplete`/`isMaterializationComplete` — but the unit tests still called `gate.isCompletedCheckL2(...)`. Result: master Cloud Build failed at `testCompile` (cannot find symbol), no image produced, and dev kept running the older tag until a follow-up test-fix commit landed on master.

Two takeaways:
1. When a refactor deletes/renames methods, grep the test tree for callers before pushing/merging — `mvn test` on the *pre-reshape* commit gives false confidence.
2. Replacement pattern for a deleted internal-method test: rewrite it as a behavior test through the public entry point (cache miss + L1 campaign-read throws → repository fallback consulted → result cached with COMPLETE TTL). Keeps the fallback coverage without exposing internals.

Related: [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]], [[MicroProfile @Fallback never fires on self-invocation]].

## Related

- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]
