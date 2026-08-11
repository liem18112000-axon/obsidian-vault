---
ai_hash: 6d3a21bb9ba91819
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- Materialize code review report
- sprint-156
- luz_docs materialize package
- kepler/sprint-156/earchive-master branch
- master branch
- C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md
- 15 ranked findings
- 9 below-cap findings
- cleanup pile
- 3 refuted candidates
- folder security-class changes
- PATCH path
- updateSecurityClasses endpoint
- materialize parent-change cascade
- PUT method
- _isPublic field
- _effectiveSecurityClassCodes field
- materialized read path
- sentinel fields
- API responses
- single-doc snapshot
- Mongo 16MB cap
- SC_MULTI_STATUS
- retryable
- rollback
- correct docs
- migration executor
- single-thread migration pipeline
- DistributionCacheException.isNotFound
- '`!=`→`==` in DistributionCacheException.isNotFound'
- '`this`→`self.getSnapshot`'
- copy rename path's SC_MULTI_STATUS catch
- quote projection keys
- missing-4th-sentinel exposure
- empty-codes divergence
- ForkJoinPool thread-safety
- luz_docs folder security-class changes have 3 entry points but only PUT cascades
  (related note)
- DistributionCacheException.isNotFound is inverted in luz_docs (related note)
- Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore
  SC_MULTI_STATUS as benign (related note)
- CDI self-invocation bypasses interceptor proxy (related note)
- jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc
  snapshots (related note)
- Mongo
- jsonstore
- CDI
- MicroProfile
- Java
source: materialize code review 2026-06-07
status: seedling
tags:
- luz-docs
- materialize
- code-review
- earchive
- sprint-156
title: Materialize code review report - sprint-156 findings index
type: observation
---

# Materialize code review report - sprint-156 findings index

Full max-effort review of the luz_docs `materialize` package (branch `kepler/sprint-156/earchive-master` vs master) lives at `C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md` — 15 ranked findings + 9 below-cap + cleanup pile + 3 refuted candidates.

Top severity (security): folder security-class changes via the PATCH path and the `updateSecurityClasses` endpoint never trigger the materialize parent-change cascade (only PUT does) → stale `_isPublic`/`_effectiveSecurityClassCodes` → wrong access on the materialized read path. Also: sentinel fields leak into API responses; single-doc snapshot breaks at Mongo's 16MB cap; SC_MULTI_STATUS wrapped as retryable → rollback reverts correct docs; migration executor infinite loop wedges the single-thread migration pipeline.

Quick wins: `!=`→`==` in DistributionCacheException.isNotFound; `this`→`self.getSnapshot`; copy rename path's SC_MULTI_STATUS catch; quote projection keys.

Check the report before re-reviewing this package — refuted candidates listed there (missing-4th-sentinel exposure, empty-codes divergence, ForkJoinPool thread-safety) should not be re-raised.

## Related

- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]
- [[DistributionCacheException.isNotFound is inverted in luz_docs]]
- [[3 Resources/Data/MongoDB/Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[3 Resources/Languages/Java/CDI and MicroProfile/CDI self-invocation bypasses interceptor proxy]]
- [[jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots]]

%% ai-graph-start %%

**Related notes:**
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[luz_docs parent-change cascade tightened with setEquals slot-differs expr to make 207 diagnostic]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]

**Relations:**
- Materialize code review report — *covers_sprint* — sprint-156
- Materialize code review report — *reviews_package* — luz_docs materialize package
- luz_docs materialize package — *compared_branch* — kepler/sprint-156/earchive-master branch
- kepler/sprint-156/earchive-master branch — *vs_branch* — master branch
- Materialize code review report — *located_at* — C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md
- C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md — *contains* — 15 ranked findings
- C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md — *contains* — 9 below-cap findings
- C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md — *contains* — cleanup pile
- C:/Users/dvtliem/Kepler/luz_docs/docs/materialize-code-review.md — *contains* — 3 refuted candidates
- folder security-class changes — *is_top_severity_finding_in* — Materialize code review report
- folder security-class changes — *occurs_via* — PATCH path
- folder security-class changes — *occurs_via* — updateSecurityClasses endpoint
- updateSecurityClasses endpoint — *does_not_trigger* — materialize parent-change cascade
- materialize parent-change cascade — *triggered_by* — PUT method
- folder security-class changes — *leads_to_stale* — _isPublic field
- folder security-class changes — *leads_to_stale* — _effectiveSecurityClassCodes field
- _isPublic field — *causes* — wrong access
- _effectiveSecurityClassCodes field — *causes* — wrong access
- wrong access — *on* — materialized read path
- sentinel fields — *leak_into* — API responses
- single-doc snapshot — *breaks_at* — Mongo 16MB cap
- SC_MULTI_STATUS — *wrapped_as* — retryable
- retryable SC_MULTI_STATUS — *leads_to* — rollback
- rollback — *reverts* — correct docs
- migration executor — *causes* — infinite loop
- infinite loop — *wedges* — single-thread migration pipeline
- Quick wins — *include_fix* — `!=`→`==` in DistributionCacheException.isNotFound
- Quick wins — *include_fix* — `this`→`self.getSnapshot`
- Quick wins — *include_fix* — copy rename path's SC_MULTI_STATUS catch
- Quick wins — *include_fix* — quote projection keys
- 3 refuted candidates — *include* — missing-4th-sentinel exposure
- 3 refuted candidates — *include* — empty-codes divergence
- 3 refuted candidates — *include* — ForkJoinPool thread-safety
- luz_docs folder security-class changes have 3 entry points but only PUT cascades (related note) — *discusses* — folder security-class changes
- DistributionCacheException.isNotFound is inverted in luz_docs (related note) — *discusses* — DistributionCacheException.isNotFound
- Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign (related note) — *discusses* — SC_MULTI_STATUS
- Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign (related note) — *discusses* — Mongo
- Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign (related note) — *discusses* — jsonstore
- CDI self-invocation bypasses interceptor proxy (related note) — *discusses* — CDI
- CDI self-invocation bypasses interceptor proxy (related note) — *discusses* — MicroProfile
- CDI self-invocation bypasses interceptor proxy (related note) — *discusses* — Java
- jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots (related note) — *discusses* — jsonstore
- jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots (related note) — *discusses* — quote projection keys
- jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots (related note) — *discusses* — Mongo 16MB cap
- jsonstore projections need quoted JSON keys and Mongo 16MB doc limit caps single-doc snapshots (related note) — *discusses* — single-doc snapshot

%% ai-graph-end %%