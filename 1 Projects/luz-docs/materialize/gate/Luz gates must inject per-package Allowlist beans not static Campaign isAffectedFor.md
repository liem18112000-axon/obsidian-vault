---
ai_hash: 1c39ec990e42f409
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- Luz gates
- Allowlist beans
- Campaign
- MaterializeAllowlist
- ParallelizeAllowlist
- Campaign::isEnabled
- Campaign.of(...).isAffectedFor(...)
- allowlist.isAllow(tenantId)
- PropertyRetriever
- MicroProfile Config
- Cloud Build
- ParallelizeGateTest
- ParallelizeGate
- NPE PropertyRetriever.config is null
- isAllow -> true stub
- facades
source: Cloud Build failure 2026-07-22
status: seedling
tags:
- luz-docs
- java
- testing
- gotcha
title: Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor
type: lesson
---

# Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor

The LUZ-156856 campaign-flag allowlist lives in per-package injectable beans (`MaterializeAllowlist`, `ParallelizeAllowlist` — the parallelize one also requires `Campaign::isEnabled`). Gates and facades must inject the bean (`allowlist.isAllow(tenantId)`), never call `Campaign.of(...).isAffectedFor(...)` statically: the static path drags in `PropertyRetriever`/MicroProfile Config at class-init and is unmockable, which broke all 7 ParallelizeGateTest cases in Cloud Build (NPE `PropertyRetriever.config is null`) after a master merge left the static call inlined in ParallelizeGate. Fix = inject the bean + one lenient `isAllow -> true` stub in the test.

%% ai-graph-start %%

**Related notes:**
- [[mockStatic ConfigProvider without getConfig stub latches null into static Config fields]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[MaterializeGate migration check falls through to repo on missing campaign]]
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier]]

**Relations:**
- Luz gates — *MUST_INJECT* — Allowlist beans
- Luz gates — *SHOULD_NOT_CALL_STATICALLY* — Campaign.of(...).isAffectedFor(...)
- Allowlist beans — *CONTAINS* — MaterializeAllowlist
- Allowlist beans — *CONTAINS* — ParallelizeAllowlist
- ParallelizeAllowlist — *REQUIRES* — Campaign::isEnabled
- Luz gates — *MUST_CALL* — allowlist.isAllow(tenantId)
- facades — *MUST_CALL* — allowlist.isAllow(tenantId)
- Campaign.of(...).isAffectedFor(...) — *DRAGS_IN* — PropertyRetriever
- Campaign.of(...).isAffectedFor(...) — *DRAGS_IN* — MicroProfile Config
- PropertyRetriever — *IS* — unmockable
- MicroProfile Config — *IS* — unmockable
- Campaign.of(...).isAffectedFor(...) — *BROKE* — ParallelizeGateTest
- ParallelizeGateTest — *RUNS_IN* — Cloud Build
- Campaign.of(...).isAffectedFor(...) — *CAUSED* — NPE PropertyRetriever.config is null
- Campaign.of(...).isAffectedFor(...) — *WAS_INLINED_IN* — ParallelizeGate
- Fix — *IS* — injecting Allowlist beans
- Fix — *INCLUDES* — isAllow -> true stub
- isAllow -> true stub — *USED_IN* — ParallelizeGateTest

%% ai-graph-end %%