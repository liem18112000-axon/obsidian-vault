---
title: "luz-docs-integration-test dev poller derives the GKE job name from the wrong id"
created: 2026-06-15
type: lesson
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, cloud-build, gke, gotcha]
---

# luz-docs-integration-test dev poller derives the GKE job name from the wrong id

The `luz-docs-integration-test` skill's MODE=dev poller can report a false TIMEOUT (exit 2) with "0 job log lines" even when the suite actually PASSED.

Root cause: it derives the GKE job name from the Cloud Build **trigger id** (`buildTriggerId`, e.g. `9973b02e...`) and polls `pod_name:dev-luz-docs-it-<trigger-id>`. But the build step actually creates the job named after the **build id** (`id`, e.g. `06c32919...`) — `dev-luz-docs-it-<build-id>`. The two ids differ, so the poller watches a pod that never exists and sees 0 lines for the whole 900s.

Recovery (get the real result yourself):
1. The true build id is the `id:` field in the trigger-run metadata (the `logUrl` also contains it), NOT the `buildTriggerId`.
2. `gcloud builds describe <build-id> --project klara-infra --region europe-west6 --format='value(status)'` -> SUCCESS/FAILURE.
3. `gcloud logging read 'resource.type="k8s_container" resource.labels.namespace_name="dev" resource.labels.pod_name:"dev-luz-docs-it-<build-id>" ("Took" OR "scenarios passed")' --project klara-nonprod --freshness=2h --order=asc --format='value(textPayload)'` for the behave summary.

Also: an in-cluster dev run is much faster than local (no kubectl port-forward overhead) — 8 recover-materialize scenarios took ~12s on dev vs ~4m40s locally.

Related: [[JSON-driven Scenario Outline pattern for luz_docs materialize integration tests]]
