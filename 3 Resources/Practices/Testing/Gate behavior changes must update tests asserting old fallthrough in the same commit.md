---
title: "Gate behavior changes must update tests asserting old fallthrough in the same commit"
created: 2026-07-21
updated: 2026-07-22
type: lesson
status: seedling
source: "luz-docs commits 3a83b5e82 / e2819386f (2026-07-22); luz_docs PR #1363 LUZ-156856 master build 72bfc3d7 (2026-07-21)"
tags: [testing, ci, mockito, gate-pattern, luz-docs, gotcha]
---

# Gate behavior changes must update tests asserting old fallthrough in the same commit

Any change that invalidates an existing test — a reshaped/renamed/deleted method, or an intentionally changed fallback path in a gate-style class (cache → service → repo chain) — must land with the test update **in the same commit**. Before pushing, grep the test tree for callers of the removed symbol and for every assertion of the old behavior. `mvn test` on the *pre-change* commit gives false confidence.

**Why it matters — the failure is delayed and misread:**
- Stale *assertion*: CI can pass on the offending commit (test not exercised, or no build run) and go red on an unrelated later commit — luz-docs 3a83b5e82 changed `MaterializeGate`'s migration check to fall through to the repo on a missing campaign record but left `MaterializeGateTest.cache_miss_no_campaign_returns_false` asserting `verifyNoInteractions(repository)`; the *next* commit (e2819386f) is where Cloud Build actually ran the suite and failed 1 of 1099 tests — easy to mistake for a flake.
- Stale *call site*: it breaks the build outright — PR #1363 inlined `isCompletedCheckL1/L2` into `isShardingComplete`/`isMaterializationComplete` while tests still called `gate.isCompletedCheckL2(...)`, so master failed at `testCompile`, produced no image, and dev kept running the older tag until a follow-up fix commit landed.

**Fix pattern:** rewrite the stale test rather than deleting it — it still documents a real code path. For a changed behavior, stub the now-reachable dependency, verify it is called, assert the new outcome. For a deleted internal method, re-express it as a behavior test through the public entry point (e.g. cache miss + L1 campaign read throws → repository fallback consulted → result cached with COMPLETE TTL).

## Related

- [[MaterializeGate migration check falls through to repo on missing campaign]]
- [[Run the full affected test package locally, not a hand-picked subset]]
- [[Hand-rolled Optional.or fallback chain replaces CDI @Fallback]]
- [[MicroProfile @Fallback never fires on self-invocation]]
