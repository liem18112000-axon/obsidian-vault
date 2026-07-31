---
ai_hash: 6d025d141489111b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz_docs_statistic
- unmaterializedDocuments
- metric
- docs
- materialize sentinel field
- sprint 158
- LUZ-155460
- June 2026
- facet
- total
- archived
- deleted
- document
- _isPublic
- _effectiveSecurityClassCodes
- _folderNames
- _folderSecurityClassCodes
- security codes
- folder
- luz_docs
- materialize pass
- backfill progress
- tenant
- JsonStoreQueryUtil.unmaterializedDocumentConditionsBuilder()
- DocumentStatisticUtils.buildFacetArrays()
- 1-minute EJB timer
- PubSub
- $facet aggregation
source: LUZ-155460 implementation, session 2026-06-11
status: seedling
tags:
- luz
- luz-docs-statistic
- materialize
- earchive
- LUZ-155460
title: luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize
  sentinel field
type: concept
---

# luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field

As of sprint 158 (LUZ-155460, June 2026) luz_docs_statistic tracks a fourth facet, `unmaterializedDocuments` (count only — the size variant was dropped), alongside total/archived/deleted. A document counts as **unmaterialized** when ANY of the four materialize sentinel fields is absent ($or of $exists:false):

- `_isPublic` — true iff doc has no own security codes AND at least one containing folder is code-free (or doc is in no folder)
- `_effectiveSecurityClassCodes` — union of doc's own codes + all folders' own∪inherited codes
- `_folderNames` — folder names parallel to folderIds order (missing folder → empty-string slot)
- `_folderSecurityClassCodes` — per-folder own∪inherited codes, parallel array to folderIds/_folderNames

These fields are written by luz_docs' materialize pass; the metric exists to monitor backfill progress — it should trend to 0 once the materialise rollout/backfill completes for a tenant. Filter lives in `JsonStoreQueryUtil.unmaterializedDocumentConditionsBuilder()`, facet wiring in `DocumentStatisticUtils.buildFacetArrays()`.

Related: [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

## Related

- [[2 Areas/Kepler/Luz/luz-docs-statistic/luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
- [[Stale-materialized detection recomputes MaterializeCompute state via $lookup inside the statistic $facet]]
- [[totalFolders needs a second aggregate because a $facet pipeline is bound to one collection]]
- [[luz_docs_statistic updates stats via 1-minute EJB timer over PubSub and $facet aggregation]]
- [[luz-docs parallelized count undercounts documents missing _shard]]

**Relations:**
- luz_docs_statistic — *tracks* — unmaterializedDocuments
- unmaterializedDocuments — *is a* — metric
- metric — *counts* — docs
- docs — *missing* — materialize sentinel field
- unmaterializedDocuments — *introduced in* — sprint 158
- sprint 158 — *associated with* — LUZ-155460
- sprint 158 — *occurred in* — June 2026
- unmaterializedDocuments — *is a* — facet
- luz_docs_statistic — *tracks* — total
- luz_docs_statistic — *tracks* — archived
- luz_docs_statistic — *tracks* — deleted
- document — *counts as unmaterialized if absent* — _isPublic
- document — *counts as unmaterialized if absent* — _effectiveSecurityClassCodes
- document — *counts as unmaterialized if absent* — _folderNames
- document — *counts as unmaterialized if absent* — _folderSecurityClassCodes
- _isPublic — *is a* — materialize sentinel field
- _effectiveSecurityClassCodes — *is a* — materialize sentinel field
- _folderNames — *is a* — materialize sentinel field
- _folderSecurityClassCodes — *is a* — materialize sentinel field
- _isPublic — *indicates no own* — security codes
- _isPublic — *indicates code-free* — folder
- _effectiveSecurityClassCodes — *is union of doc's own codes and folder's* — security codes
- _folderNames — *is parallel to* — folderIds order
- _folderSecurityClassCodes — *is parallel array to* — folderIds/_folderNames
- materialize sentinel field — *written by* — luz_docs' materialize pass
- metric — *monitors* — backfill progress
- backfill progress — *completes for* — tenant
- JsonStoreQueryUtil.unmaterializedDocumentConditionsBuilder() — *contains* — filter
- DocumentStatisticUtils.buildFacetArrays() — *contains* — facet wiring
- luz_docs_statistic — *updates stats via* — 1-minute EJB timer
- luz_docs_statistic — *updates stats via* — PubSub
- luz_docs_statistic — *updates stats via* — $facet aggregation

%% ai-graph-end %%