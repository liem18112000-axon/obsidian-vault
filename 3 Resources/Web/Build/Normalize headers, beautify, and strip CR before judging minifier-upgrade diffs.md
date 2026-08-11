---
ai_hash: e7d5c28215b1fc10
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities: []
source: LEO CDP migration R6, 2026-06-07
status: seedling
tags:
- minification
- closure-compiler
- diff
- technique
title: Normalize headers, beautify, and strip CR before judging minifier-upgrade diffs
type: howto
---

# Normalize headers, beautify, and strip CR before judging minifier-upgrade diffs

When a minifier upgrade changes artifact checksums, judge the diff in three normalization steps before sounding alarms: (1) strip volatile headers (version/timestamp lines), (2) beautify both sides (npx js-beautify), (3) diff with --strip-trailing-cr - minifier versions often differ only in emitted line endings. In LEO CDP, a scary '4 files / ~3000 beautified diff lines' from the gradle-minify-plugin 1.3.2->2.1.1 bump collapsed to exactly 2 real lines: one regex-literal->RegExp() equivalent transform (newer Closure), one stale-artifact catch-up that was a genuine source edit. Also run 'node --check' on each minified file as a free parse gate.

## Related

- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]

%% ai-graph-start %%

**Related notes:**
- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]
- [[LEO CDP AutoBuildForDeployment minifies admin JS only - observer tracker needs explicit task]]
- [[Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper bumps]]

%% ai-graph-end %%