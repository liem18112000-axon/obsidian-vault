---
title: "Split Terraform: cluster-provisioning state separate from in-cluster workload state"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26 feat/appsflyer-push-layer; terraform/push"
tags: [terraform, kubernetes, iac, gotcha, vngcloud, leo-cdp]
---

# Split Terraform: cluster-provisioning state separate from in-cluster workload state

Do NOT create a Kubernetes cluster AND deploy in-cluster resources (Deployment/Service/Ingress…) in the **same** Terraform configuration/state.

Why: the `kubernetes` (or `helm`) provider must be configurable at **plan time**, but its credentials (host, CA, token / kubeconfig) only exist **after** the cluster resource is created in that same apply. Terraform evaluates provider blocks before/independently of resource creation, so a provider configured from `resource.cluster.kubeconfig` is the classic bootstrap anti-pattern — it works on a clean apply by luck sometimes, then breaks on refresh/destroy/re-plan with 'provider configuration unknown' or connection errors.

Fix: **two layers, two states.**
- Cluster layer: the cloud provider (e.g. vngcloud) provisions the cluster + node groups. Its own state.
- App/workload layer: the `kubernetes` provider, configured from a **kubeconfig the operator/CI supplies** (`config_path = var.kubeconfig_path`), deploys the workloads. Its own state key.

Apply order: provision cluster → fetch kubeconfig → apply workload. In CI, write the base64 KUBE_CONFIG secret to a file and pass its path as TF_VAR_kubeconfig_path. This also lets the app redeploy frequently without touching (or risking) the cluster.

Bonus k8s-on-Terraform patterns used alongside this: put the HPA-owned `replicas` under `lifecycle { ignore_changes = [spec[0].replicas] }` so Terraform and the autoscaler don't fight; treat ingress-controller + cert-manager as platform prerequisites (inputs), not per-app resources.

Context: Leo CDP AppsFlyer push receiver, terraform/push/ on VNGCloud VKS. See [[A webhook receiver deploys as an always-on service, not a scheduled job]].

## Related

- [[A webhook receiver deploys as an always-on service]]
- [[not a scheduled job]]
