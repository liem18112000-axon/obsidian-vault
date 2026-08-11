---
ai_hash: cee4a0e265e09e7a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration Phase 0, 2026-06-06
status: seedling
tags:
- migration
- build
- baseline
- gotcha
- leo-cdp
title: Baseline-diff gates must compare post-build to post-build when artifacts are
  committed
type: lesson
---

# Baseline-diff gates must compare post-build to post-build when artifacts are committed

When a repo commits generated artifacts (e.g. minified JS) and a migration gate diffs build output before/after a toolchain change, the reference must be the **post-build state under the old toolchain**, never the committed files — committed artifacts can be stale (edited sources without regeneration).

Found in LEO CDP: the committed `common-resources-min/*.js` differed from a fresh Gradle 6.9.4 rebuild in 4 files (real content lines, not just the version header) — the committed copies were outdated. Diffing Gradle-9 output against the *committed* files would have produced false alarms; diffing against the fresh 6.9.4 output isolates exactly what the toolchain change caused.

Practical recipe: build on old toolchain → checksum artifacts (excluding volatile headers like timestamps) → build on new toolchain → compare checksums. Revert artifact churn from the working tree so it doesn't pollute migration commits.

## Related

- [[Decouple runtime JDK from bytecode target when migrating Java versions]]

%% ai-graph-start %%

**Related notes:**
- [[Normalize headers, beautify, and strip CR before judging minifier-upgrade diffs]]
- [[LEO CDP AutoBuildForDeployment minifies admin JS only - observer tracker needs explicit task]]
- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]

%% ai-graph-end %%