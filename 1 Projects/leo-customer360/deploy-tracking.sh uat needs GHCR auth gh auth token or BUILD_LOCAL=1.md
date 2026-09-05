---
title: "deploy-tracking.sh uat needs GHCR auth: gh auth token or BUILD_LOCAL=1"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-31 uat deploy"
tags: [leo-customer360, ghcr, deployment, terraform, gotcha]
---

# deploy-tracking.sh uat needs GHCR auth: gh auth token or BUILD_LOCAL=1

Deploying `deployments/server/deploy-tracking.sh <env>` in leo-customer360 pulls the CI image `ghcr.io/leo-cdp/leo-customer360/data-tracking-api`, which is a **PRIVATE** GHCR package. Without registry auth the pull fails with `Error response from daemon: error from registry: denied`.

## Two fixes

- **Auth the pull** with the logged-in GitHub CLI (no PAT needed if `gh` has `read:packages`):
  ```bash
  GHCR_USER=<gh-username> GHCR_TOKEN="$(gh auth token)" ./deploy-tracking.sh uat
  ```
  The script does `docker login ghcr.io -u $GHCR_USER --password-stdin` only when `GHCR_TOKEN` is set. Verify access first with `docker manifest inspect <image@sha>` after `gh auth token | docker login ghcr.io -u <user> --password-stdin`.
- **Build on the VM instead** (no registry access at all):
  ```bash
  BUILD_LOCAL=1 ./deploy-tracking.sh uat
  ```

## Also required
The Terraform **S3 backend on vStorage** needs `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` in the environment to read state (`terraform workspace select` / `terraform output servers`). The script auto-sources `deployments/server/.env`, which carries them — but a bare manual `terraform` call outside the script fails with "No valid credential sources found" until you `set -a; source ./.env`.

Related: [[Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB]].

## Related

- [[Scale one uvicorn service into N replicas on one VM with a docker bridge + local nginx LB]]
