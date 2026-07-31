---
title: "Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor"
created: 2026-07-22
type: lesson
status: seedling
source: "Cloud Build failure 2026-07-22"
tags: [luz-docs, java, testing, gotcha]
---

# Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor

The LUZ-156856 campaign-flag allowlist lives in per-package injectable beans (`MaterializeAllowlist`, `ParallelizeAllowlist` — the parallelize one also requires `Campaign::isEnabled`). Gates and facades must inject the bean (`allowlist.isAllow(tenantId)`), never call `Campaign.of(...).isAffectedFor(...)` statically: the static path drags in `PropertyRetriever`/MicroProfile Config at class-init and is unmockable, which broke all 7 ParallelizeGateTest cases in Cloud Build (NPE `PropertyRetriever.config is null`) after a master merge left the static call inlined in ParallelizeGate. Fix = inject the bean + one lenient `isAllow -> true` stub in the test.
