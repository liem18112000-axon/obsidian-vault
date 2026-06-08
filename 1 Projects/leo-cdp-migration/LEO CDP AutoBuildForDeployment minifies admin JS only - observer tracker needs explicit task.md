---
title: "LEO CDP AutoBuildForDeployment minifies admin JS only - observer tracker needs explicit task"
created: 2026-06-07
type: observation
status: seedling
source: "LEO CDP migration R6 scoping, 2026-06-07"
tags: [leo-cdp, minify, cdn, closure-compiler, migration]
---

# LEO CDP AutoBuildForDeployment minifies admin JS only - observer tracker needs explicit task

In LEO CDP, the build task `AutoBuildForDeployment` runs only `minifyJsAdminResource` (admin UI JS) — `minifyJsLeoObserver` (the CDN-published tracker) must be run explicitly. Consequence for the Gradle-9/minify-plugin-2.1.1 migration: the jsDelivr-published `leo.observer.min.js` is untouched by a normal build (zero CDN risk from the migration build itself), but the risk is *deferred*, not eliminated — the FIRST future run of `minifyJsLeoObserver` under plugin 2.1.1/newer Closure will produce different output and needs tracker QA then. The 4 admin-UI files that did change (`leo.admin.common.js`, `leocdp.chatbot.js`, `leocdp.core-admin.js`, `leocdp.finance.js`) ship inside the build output and are served by the admin app, so they are testable entirely on a local/staging instance.

## Related

- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]
