---
title: "klara-prod PROD logging access: gcloud logging read only, no kubectl"
created: 2026-06-30
type: lesson
status: seedling
source: "PROD jwt-service investigation 2026-06-30"
tags: [gcp, klara, prod, gcloud, kubernetes, access]
---

# klara-prod PROD logging access: gcloud logging read only, no kubectl

On GCP, the PROD project is **klara-prod** (GKE `cluster_name=klara-prod`, `namespace_name=prod`, `location=europe-west6-a`). The everyday gcloud account `lam.nguyen@axonactive.com` has **Cloud Logging read** on klara-prod but is **denied** `container.clusters.list` / `run.services.list` / kubectl there.

**Why it matters:** any PROD investigation must be driven entirely through `gcloud logging read` — you cannot `kubectl get pods`, list clusters, or list Cloud Run services on prod with that account. Discover pod names, replica counts, restarts, etc. *from the logs themselves* (e.g. distinct `resource.labels.pod_name`, k8s `events` logName) rather than from the control plane.

The `google-skill-*` helper skills default to `klara-nonprod` + `dev`; for prod override `CLUSTER_PROJECT=klara-prod CLUSTER_NAME=klara-prod NAMESPACE=prod`. Other projects: klara-nonprod (dev/test/etc.), klara-performance, klara-infra, klara-repo.

See [[Istio DC response_flag with round latency = caller read timeout]] for the main prod log-reading technique.

## Related

- [[Istio DC response_flag with round latency = caller read timeout]]
