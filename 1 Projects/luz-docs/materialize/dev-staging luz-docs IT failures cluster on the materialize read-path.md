---
ai_hash: 067775b4131242a8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities:
- dev-staging
- master
- tenant 6873c725-bf58-4c9e-b9bf-173059b692cd
- behave IT suite
- materialize read-path
- GKE Job
- Error pods
- get_document
- folder/update_security_classes
- search_document facets
- update_patch
- enrich_document
- materialize-regression
- materialize sentinel fields
- _folderNames
- _effectiveSecurityClassCodes
- _updatedDate
- _isPublic
- dev-staging documents collection
- _id_ index
- materialize indexes
- unindexed collscans
- sentinel-field gaps
- luz-docs-integration-test MODE=dev
- job logs
- Cloud Logging filter
- dev-staging pod logs
- kubectl
- behave footer
- dev-staging tenants
- mongo cluster
- dev
- LUZ-152936
- LUZ-154613
source: session 2026-06-17
status: seedling
tags:
- luz-docs
- materialize
- integration-test
- dev-staging
- LUZ-152936
title: dev-staging luz-docs IT failures cluster on the materialize read-path
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[luz-docs 2026-06-11 dev integration run failure clusters]]
- [[dev-staging tenants live on a separate mongo cluster from dev]]
- [[luz-docs-integration-test dev poller derives the GKE job name from the wrong id]]
- [[Materialize code review report - sprint-156 findings index]]
- [[luz-docs-it-staging-trigger-poller-mismatch]]

**Relations:**
- behave IT suite — *runs_on* — dev-staging
- dev-staging — *has_tenant* — tenant 6873c725-bf58-4c9e-b9bf-173059b692cd
- behave IT suite — *fails_on* — materialize read-path
- behave IT suite — *exits_non_zero* — 
- GKE Job — *retries_due_to* — behave IT suite
- GKE Job — *generates* — Error pods
- GKE Job — *does_not_reach_completion* — 
- get_document — *is_a_failure_cluster* — 
- get_document — *has_status* — errored
- folder/update_security_classes — *is_a_failure_cluster* — 
- folder/update_security_classes — *has_status* — errored
- search_document facets — *is_a_failure_cluster* — 
- search_document facets — *has_status* — failed
- update_patch — *is_a_failure_cluster* — 
- update_patch — *has_status* — failed
- enrich_document — *is_a_failure_cluster* — 
- enrich_document — *has_status* — errored
- get_document — *is_fingerprint_for* — materialize-regression
- folder/update_security_classes — *is_fingerprint_for* — materialize-regression
- search_document facets — *is_fingerprint_for* — materialize-regression
- update_patch — *is_fingerprint_for* — materialize-regression
- enrich_document — *is_fingerprint_for* — materialize-regression
- materialize sentinel fields — *includes* — _folderNames
- materialize sentinel fields — *includes* — _effectiveSecurityClassCodes
- materialize sentinel fields — *includes* — _updatedDate
- materialize sentinel fields — *includes* — _isPublic
- materialize sentinel fields — *drive* — failure clusters
- dev-staging documents collection — *has_only* — _id_ index
- dev-staging documents collection — *lacks* — materialize indexes
- lack of materialize indexes — *causes* — unindexed collscans
- lack of materialize indexes — *causes* — sentinel-field gaps
- unindexed collscans — *surface_on* — materialize read-path
- sentinel-field gaps — *surface_on* — materialize read-path
- luz-docs-integration-test MODE=dev — *polled* — job logs
- luz-docs-integration-test MODE=dev — *timed_out_at* — 900s
- Cloud Logging filter — *did_not_match* — dev-staging pod logs
- kubectl — *reads* — dev-staging pod logs
- dev-staging pod logs — *contains* — behave footer
- materialize read-path — *is_related_to* — LUZ-152936
- materialize read-path — *is_related_to* — LUZ-154613
- dev-staging tenants — *live_on* — mongo cluster
- mongo cluster — *is_separate_from* — dev

%% ai-graph-end %%