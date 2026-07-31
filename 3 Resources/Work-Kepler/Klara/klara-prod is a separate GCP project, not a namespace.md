---
title: "klara-prod is a separate GCP project, not a namespace"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs prod migration incident investigation, 2026-07-10"
tags: [klara, gcp, gke, logging, prod, gotcha]
---

# klara-prod is a separate GCP project, not a namespace

klara-prod is a completely separate GCP project from klara-nonprod — it is not a namespace variant of the same project. The googl-skill-gke-logs / gke-monitor skill defaults (CLUSTER_PROJECT=klara-nonprod, CLUSTER_NAME=klara-nonprod) only cover dev/test/staging/performance; they silently return zero results (not an error) when pointed at prod namespaces, because prod lives in its own GKE cluster inside its own project.

To query prod logs, override explicitly: `CLUSTER_PROJECT=klara-prod CLUSTER_NAME=klara-prod NAMESPACE=prod`.

Confirmed 2026-07-10 while investigating a prod migration failure: querying klara-nonprod for an exact log line returned nothing; the same query against klara-prod matched immediately. `gcloud projects list` shows klara-prod as a distinct project alongside klara-nonprod, klara-performance, klara-infra, klara-repo.

Also: the account used (lam.nguyen@axonactive.com) could run `gcloud logging read` against klara-prod successfully, but `gcloud container clusters list --project=klara-prod` returned 403 (no `container.clusters.list` permission) and no kubectl context for klara-prod exists locally. So Cloud Logging read access and GKE cluster/kubectl access are separate grants — you can read prod logs without being able to port-forward into prod pods or query prod Mongo directly.
