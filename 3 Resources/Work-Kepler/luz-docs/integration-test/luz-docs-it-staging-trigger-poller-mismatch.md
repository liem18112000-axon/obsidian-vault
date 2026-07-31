---
ai_hash: 480ec8d7a8425415
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
tags:
- luz
- luz-docs-integration-test
- kepler
- cloud-build
- gke
- gotcha
title: luz-docs IT dev-staging trigger — poller namespace/job mismatch
---

# luz-docs IT `dev-staging` trigger vs. the run_it.sh poller

Running the IT via the **`dev-luz-docs-integration-test-scheduleEvent-staging`**
Cloud Build trigger (MODE=dev) makes the `luz-docs-integration-test` skill's
poller **time out with `job log lines so far: 0`** even though the suite runs fine.

## Why

The skill (`run_it.sh`) assumes the `dev` defaults:
- namespace `dev`
- job/pod name `dev-luz-docs-it-<BUILD_UUID>`

But the **staging** trigger's `cloudbuild.yaml` substitutions are different:
- `_GKE_NAMESPACE = dev-staging`
- `_GKE_JOB_NAME = dev-staging-luz-docs-it-<BUILD_UUID>`

So the poller watches the wrong namespace + job prefix → 0 lines → timeout (exit 2).
This is **not** a test failure.

Extra trap: GKE truncates the pod name to **63 chars**. `dev-staging-luz-docs-it-`
(24) + a full build UUID (36) already = 60, so the trailing chars of the UUID get
cut before the `-<podsuffix>`. A Cloud Logging filter on the *full* build UUID
(`...e65`) matches nothing — filter on a **truncated prefix**
(e.g. `pod_name:"dev-staging-luz-docs-it-4b3c20c0"`).

## How to get staging results manually

```bash
gcloud logging read \
  'resource.type="k8s_container" AND resource.labels.pod_name:"dev-staging-luz-docs-it-<SHORT_BUILD_PREFIX>"' \
  --project=klara-nonprod --freshness=90m --limit=2000 --order=asc \
  --format="value(textPayload)" | grep -E "Took |scenarios? passed|\[FAILED\]|\[PASSED\]"
```

Find the exact namespace/job from the build:
`gcloud builds describe <BUILD_ID> --region=europe-west6 --project=klara-infra --format="yaml(substitutions)"`
→ read `_GKE_NAMESPACE`, `_GKE_JOB_NAME`.

## Confirmed facts

- The staging trigger **honors** `_DEFAULT_CMD` (e.g. `behave ... features/statistics` ran only that feature).
- `--branch=<feature-branch>` works: the build checks out the pushed branch revision
  (so push before triggering — Cloud Build reads Bitbucket, not the local tree).
- Beware unrelated **scheduled `dev` runs** in namespace `dev` happening concurrently —
  don't mistake `dev-luz-docs-it-*` for your `dev-staging-luz-docs-it-*` job.

Related: [[luz-docs-statistic-get-latest-endpoint]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs-integration-test dev poller derives the GKE job name from the wrong id]]
- [[Shipping luz_docs_statistic trigger is docs-statistic-service and dev runs a Deployment, not a StatefulSet]]
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]
- [[luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]

%% ai-graph-end %%