---
title: "Materialize code review report - sprint-156 findings index"
created: 2026-06-07
type: observation
status: seedling
source: "materialize code review 2026-06-07"
tags: [luz-docs, materialize, code-review, earchive, sprint-156]
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
