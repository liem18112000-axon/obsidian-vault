---
title: "luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field"
created: 2026-06-11
type: concept
status: seedling
source: "LUZ-155460 implementation, session 2026-06-11"
tags: [luz, luz-docs-statistic, materialize, earchive, LUZ-155460]
---

# luz_docs_statistic unmaterializedDocuments metric counts docs missing any materialize sentinel field

As of sprint 158 (LUZ-155460, June 2026) luz_docs_statistic tracks a fourth facet, `unmaterializedDocuments` (count only — the size variant was dropped), alongside total/archived/deleted. A document counts as **unmaterialized** when ANY of the four materialize sentinel fields is absent ($or of $exists:false):

- `_isPublic` — true iff doc has no own security codes AND at least one containing folder is code-free (or doc is in no folder)
- `_effectiveSecurityClassCodes` — union of doc's own codes + all folders' own∪inherited codes
- `_folderNames` — folder names parallel to folderIds order (missing folder → empty-string slot)
- `_folderSecurityClassCodes` — per-folder own∪inherited codes, parallel array to folderIds/_folderNames

These fields are written by luz_docs' materialize pass; the metric exists to monitor backfill progress — it should trend to 0 once the materialise rollout/backfill completes for a tenant. Filter lives in `JsonStoreQueryUtil.unmaterializedDocumentConditionsBuilder()`, facet wiring in `DocumentStatisticUtils.buildFacetArrays()`.

Related: [[luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet aggregation]]

## Related

- [[luz_docs_statistic updates stats via 1-minute EJB timer over Pub/Sub and $facet aggregation]]
