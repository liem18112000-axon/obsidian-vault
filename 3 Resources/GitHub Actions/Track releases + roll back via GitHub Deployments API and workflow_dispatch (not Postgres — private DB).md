---
title: "Track releases + roll back via GitHub Deployments API and workflow_dispatch (not Postgres — private DB)"
created: 2026-08-21
type: howto
status: seedling
source: "session 2026-08-21, leo-customer360 release tracking"
tags: [github-actions, deployments-api, rollback, workflow-dispatch, cd, release-management]
---

# Track releases + roll back via GitHub Deployments API and workflow_dispatch (not Postgres — private DB)

For a deploy pipeline that runs from CI runners + operator laptops (outside the VPC), the release **ledger** and **rollback** are best built on GitHub, not your own DB:

**Rollback** = deploy an older immutable image tag. If the deploy scripts already resolve an `IMAGE_TAG` (env > overlay > latest), rollback is just `IMAGE_TAG=v1.2.3 ./deploy-all.sh …` locally, and a **`workflow_dispatch`** trigger on the CD workflow (inputs: environment, image_tag, services) makes it a UI/`gh workflow run` action. Roll back to `sha-<git>` (per-service) or `vX.Y.Z` (atomic, all services) — NEVER `latest` (mutable). Keep GHCR retention from pruning tagged versions.

**Ledger** = record every deploy (manual + CD) to the **GitHub Deployments API** via `gh api POST /repos/{repo}/deployments` (+ a deployment status). Put the call in a helper sourced by the deploy scripts so BOTH manual and CD are captured (CD runs the same scripts). Detect source with `[ -n "$GITHUB_ACTIONS" ]`. The repo **Environments tab** is a free history UI; the API is queryable for a custom UI later. Make it best-effort (missing gh/token must never fail a deploy).

**Why not Postgres (the obvious choice):** the managed DB sat on a **private VPC IP** unreachable from the CI runner or a laptop where the deploy orchestrator runs — only the VMs can reach it. So orchestrator-side Postgres writes are impossible without routing through a VM. GitHub Deployments is reachable from everywhere with zero infra.

Gotchas: `POST /deployments` needs `required_contexts: []` + `auto_merge:false` or GitHub gates on commit statuses; CI needs `permissions: deployments: write` + `GH_TOKEN`; build the JSON body with `json.dumps` (python) to escape actor/desc safely. Implemented in leo-customer360 `lib/record_deploy.sh` + `cd.yml` workflow_dispatch. Related: [[Chain a CD workflow after CI with workflow_run, gating on conclusion and ref]].

## Related

- [[Chain a CD workflow after CI with workflow_run]]
- [[gating on conclusion and ref]]
