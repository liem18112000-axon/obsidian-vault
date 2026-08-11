---
ai_hash: 6082e86437178b7b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- LEO CDP
- AutoBuildForDeployment
- minifyJsAdminResource
- admin UI JS
- minifyJsLeoObserver
- CDN-published tracker
- leo.observer.min.js
- jsDelivr
- Gradle 9
- minify-plugin 2.1.1
- Closure
- normal build
- tracker QA
- leo.admin.common.js
- leocdp.chatbot.js
- leocdp.core-admin.js
- leocdp.finance.js
- admin-UI files
- build output
- admin app
- local/staging instance
- Baseline-diff gates
source: LEO CDP migration R6 scoping, 2026-06-07
status: seedling
tags:
- leo-cdp
- minify
- cdn
- closure-compiler
- migration
title: LEO CDP AutoBuildForDeployment minifies admin JS only - observer tracker needs
  explicit task
type: observation
---

# LEO CDP AutoBuildForDeployment minifies admin JS only - observer tracker needs explicit task

In LEO CDP, the build task `AutoBuildForDeployment` runs only `minifyJsAdminResource` (admin UI JS) — `minifyJsLeoObserver` (the CDN-published tracker) must be run explicitly. Consequence for the Gradle-9/minify-plugin-2.1.1 migration: the jsDelivr-published `leo.observer.min.js` is untouched by a normal build (zero CDN risk from the migration build itself), but the risk is *deferred*, not eliminated — the FIRST future run of `minifyJsLeoObserver` under plugin 2.1.1/newer Closure will produce different output and needs tracker QA then. The 4 admin-UI files that did change (`leo.admin.common.js`, `leocdp.chatbot.js`, `leocdp.core-admin.js`, `leocdp.finance.js`) ship inside the build output and are served by the admin app, so they are testable entirely on a local/staging instance.

## Related

- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]

%% ai-graph-start %%

**Related notes:**
- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]
- [[Normalize headers, beautify, and strip CR before judging minifier-upgrade diffs]]
- [[Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

**Relations:**
- AutoBuildForDeployment — *runs* — minifyJsAdminResource
- AutoBuildForDeployment — *does not run* — minifyJsLeoObserver
- minifyJsAdminResource — *minifies* — admin UI JS
- minifyJsLeoObserver — *minifies* — CDN-published tracker
- CDN-published tracker — *is* — leo.observer.min.js
- jsDelivr — *publishes* — leo.observer.min.js
- minifyJsLeoObserver — *needs* — explicit task
- Gradle 9 — *is part of* — migration
- minify-plugin 2.1.1 — *is part of* — migration
- minify-plugin 2.1.1 — *uses* — Closure
- leo.observer.min.js — *is untouched by* — normal build
- minifyJsLeoObserver — *with plugin 2.1.1/newer Closure produces* — different output
- minifyJsLeoObserver — *with plugin 2.1.1/newer Closure needs* — tracker QA
- leo.admin.common.js — *is an* — admin-UI file
- leocdp.chatbot.js — *is an* — admin-UI file
- leocdp.core-admin.js — *is an* — admin-UI file
- leocdp.finance.js — *is an* — admin-UI file
- admin-UI files — *changed* — true
- admin-UI files — *ship inside* — build output
- admin app — *serves* — admin-UI files
- admin-UI files — *are testable on* — local/staging instance
- Baseline-diff gates — *is related to* — this topic

%% ai-graph-end %%