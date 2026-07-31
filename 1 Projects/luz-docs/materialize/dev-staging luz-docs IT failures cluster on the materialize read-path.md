---
title: "dev-staging luz-docs IT failures cluster on the materialize read-path"
created: 2026-06-17
type: observation
status: seedling
source: "session 2026-06-17"
tags: [luz-docs, materialize, integration-test, dev-staging, LUZ-152936]
---

# dev-staging luz-docs IT failures cluster on the materialize read-path

On dev-staging at master (tenant 6873c725-bf58-4c9e-b9bf-173059b692cd), the behave IT suite finishes (Took ~22min) but exits non-zero: **6 failed + 16 errored scenarios**, all clustered on the materialize read-path. Because behave exits non-zero, the GKE Job keeps retrying (multiple Error pods), so the job never reaches `0/1 → Completed`.

Failure clusters (use as a materialize-regression fingerprint):
- **get_document** (errored): sort-by-name asc/desc, filter-by-folder + folder names, includeDeleted, skipSecurityClasses, excludeTotalCount
- **folder/update_security_classes** (errored): add/remove on folder + children, inherited-class propagation to child
- **search_document facets** (failed): normal field, multi-facet (fields+operators), filter-to-narrow
- **update_patch** (failed): scalar add to securityClassCodes tail
- **enrich_document** (errored): URGENT priority x3

Hypothesis: these are exactly the paths driven by the materialize sentinel fields `_folderNames`, `_effectiveSecurityClassCodes`, `_updatedDate`, `_isPublic`. The dev-staging `documents` collection had **only the `_id_` index** (no materialize indexes) → unindexed collscans + any sentinel-field gaps surface here first.

Skill gotcha: `luz-docs-integration-test` MODE=dev polled job logs and saw 0 lines / timed out at 900s even though the suite was running fine — its Cloud Logging filter did not match the dev-staging pod logs. Read the pod logs directly (`kubectl -n dev-staging logs <pod>`) to get the behave footer.

Related: [[dev-staging tenants live on a separate mongo cluster from dev]], LUZ-152936 materialize rollout, LUZ-154613 shard fan-out.

## Related

- [[dev-staging tenants live on a separate mongo cluster from dev]]
