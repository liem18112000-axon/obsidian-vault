---
title: "leo-customer360 CD builds images on the VM instead of pulling from GHCR (CI/CD gap)"
created: 2026-08-20
type: observation
status: seedling
source: "session 2026-08-20, deployments/ review"
tags: [leo-customer360, cd, ghcr, deployment, terraform, vngcloud]
---

# leo-customer360 CD builds images on the VM instead of pulling from GHCR (CI/CD gap)

As of 2026-08-20, the `leo-customer360` deploy scripts under `deployments/` do **not** consume images from GitHub Container Registry at all. All four app services — `customer360-api` (`server/deploy-api.sh`), `backend-system` (`server/deploy-backend.sh`), `ads-server` (`ads-server/deploy-ads.sh`), `frontend-admin` (`frontend/deploy-frontend.sh`) — use the same pattern: `tar -czf - <svc> | ssh … tar -xzf -` to ship source to the VM, then `docker build -t <name> /opt/c360/<svc>` and `docker run` **on the box**.

Meanwhile CI (`.github/workflows/ci.yml`) builds and pushes `ghcr.io/leo-cdp/leo-customer360/<service>` tagged `sha-<full-git-sha>` (from `type=sha,format=long`) plus `latest`, but **only on `main`** (`push:` gated to `refs/heads/main`); branches/PRs are build-only.

**The gap:** CI produces immutable SHA-tagged images in GHCR that CD never uses — CD rebuilds from source on the VM instead. Wiring true CD means changing the deploy scripts to `docker pull` a resolved GHCR reference per env instead of building (see [[Get the newest GHCR image tag or digest via gh api packages container versions]]), and — for a release→prod flow — adding a tag/release trigger + semver tag to CI (currently there is no version-tag build). Environments are Terraform workspaces + `overlays/<env>.tfvars`; only the **uat** overlay is provisioned so far (prod is documented as "to be added"). Orchestrated by `deploy-all.sh <uat|prod>` (15 ordered steps).

## Related

- [[Get the newest GHCR image tag or digest via gh api packages container versions]]
