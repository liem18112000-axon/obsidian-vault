---
title: "Gate behavior changes must update tests asserting old fallthrough in the same commit"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22, luz-docs commit 3a83b5e82 / e2819386f"
tags: [testing, mockito, ci, gate-pattern]
---

# Gate behavior changes must update tests asserting old fallthrough in the same commit

When a commit intentionally changes fallback/fallthrough behavior in a gate-style class (cache -> service -> repo chain), grep for and update every test that asserts the OLD fallthrough behavior in the SAME commit -- otherwise CI passes on that commit (if the changed test isn't exercised or the build isn't run) and fails later on an unrelated, subsequent commit, which then looks like a flaky/unrelated test failure instead of what it actually is: a stale assertion left over from the earlier behavior change.

Concrete case: luz-docs commit 3a83b5e82 changed MaterializeGate's migration-check to fall through to the repo on a missing campaign record (see [[MaterializeGate migration check falls through to repo on missing campaign]]), but didn't update `MaterializeGateTest.cache_miss_no_campaign_returns_false`, which still asserted `verifyNoInteractions(repository)`. The very next commit (e2819386f) is the one whose Cloud Build actually ran the full suite and failed on it -- 1 of 1099 tests, easy to mistake for a flake at a glance.

Fix pattern: rename/rewrite the stale test to assert the new behavior (stub the now-reachable dependency, verify it's called, assert the new expected outcome) rather than deleting it -- it still documents a real code path.

## Related

- [[MaterializeGate migration check falls through to repo on missing campaign]]
