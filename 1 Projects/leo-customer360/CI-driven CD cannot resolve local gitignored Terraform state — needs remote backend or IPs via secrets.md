---
title: "CI-driven CD cannot resolve local gitignored Terraform state — needs remote backend or IPs via secrets"
created: 2026-08-20
type: observation
status: seedling
source: "session 2026-08-20, CD run 32384496374"
tags: [leo-customer360, cd, terraform, state, ci, gotcha]
---

# CI-driven CD cannot resolve local gitignored Terraform state — needs remote backend or IPs via secrets

The leo-customer360 deploy scripts resolve VM IPs / DB host from **local** Terraform state (`terraform output` in the `server`/`postgres`/`cache` modules, workspace `uat`). That state is **gitignored** (`server/.gitignore`: `*.tfstate`, `terraform.tfstate.d/`, `.terraform/`) — it exists only on the operator machine, not in git.

Consequence: running the same scripts from a GitHub Actions runner (fresh checkout, no state) fails at `terraform workspace select uat` → **"no uat server workspace"**, so cd.yml cannot deploy even with `init -upgrade` (init downloads providers but does not create the workspace or restore state). Confirmed in CD run 32384496374 (2026-08-20): select job OK (uat), deploy job failed here. All prior blockers were already fixed (exit 126 → `bash deploy-all.sh`; secrets set).

Fix options (architectural, pick one):
1. **Remote Terraform backend** (proper) — move `server`/`postgres`/`cache` state to a shared backend (vStorage/S3 or Terraform Cloud) so CI can read it.
2. **Bypass TF in CI** — pass VM IPs + DB host + redis host as GitHub secrets/vars and add env overrides to the app scripts so they skip `terraform output` when provided. `deploy-backend.sh` already honors a `BASTION` override; `deploy-api.sh`/`deploy-ads.sh`/`deploy-frontend.sh` would need the same for their host + DB host.
3. **Self-hosted runner** on the operator network that already holds the local state (least clean).
Do NOT commit tfstate (contains secrets; deliberately gitignored). Context: [[leo-customer360 CD deploys app containers to vServers only, never the vDB/vLB/vStorage Terraform]].

## Related

- [[leo-customer360 CD deploys app containers to vServers only]]
- [[never the vDB/vLB/vStorage Terraform]]
