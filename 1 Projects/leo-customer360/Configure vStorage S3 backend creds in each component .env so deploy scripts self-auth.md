---
title: "Configure vStorage S3 backend creds in each component .env so deploy scripts self-auth"
created: 2026-08-22
type: howto
status: seedling
source: "session 2026-08-22"
tags: [leo-customer360, terraform, vstorage, s3-backend, deployment, env]
---

# Configure vStorage S3 backend creds in each component .env so deploy scripts self-auth

To make leo-customer360 `deploy-*.sh` run without a manual `export AWS_ACCESS_KEY_ID/SECRET`, put the vStorage S3 backend creds in each component's `.env`.

## Why it works
Every deploy script (server, ads-server, frontend, monitoring, proxy, sso, storage, cache, postgres, load_balancer) runs `set -a; source ./.env; set +a` — `set -a` **exports** the sourced vars to the terraform child process. So two lines in each `.env`:
```
AWS_ACCESS_KEY_ID=<vStorage key>
AWS_SECRET_ACCESS_KEY=<vStorage secret>
```
let terraform's S3 backend authenticate.

## Key points
- **Must be `.env`, NOT `terraform.tfvars`** — a Terraform *backend* block cannot read tfvars/input variables; it only reads env vars + literal backend config. tfvars still supply the PROVIDER creds (vngcloud `client_id`/`client_secret`, `db_password`), so a **minimal `.env` with only the two AWS_* lines is sufficient** for modules whose other secrets live in tfvars.
- The export **propagates to sibling terraform reads** — e.g. `deploy-api.sh` sources `server/.env` then does `cd ../postgres && terraform output`; the child inherits the exported AWS_*.
- The vStorage key/secret already live in `deployments/storage/.env` as `TF_VAR_access_key`/`TF_VAR_secret_key` (the storage module's aws_s3 PROVIDER inputs) — reuse those exact values.
- `.env` is git-ignored in every module dir, so the secret stays out of git.
- **Universal single-file alternative:** `~/.aws/credentials` with a `[default]` profile (aws_access_key_id / aws_secret_access_key) — the S3 backend's AWS SDK reads it for ALL modules with zero `.env` edits (backend has `skip_credentials_validation=true`, so no STS).

## The orchestrator has its OWN creds path (don't confuse the two)
`deploy-all.sh` sources `deployments/lib/tfstate.sh` and calls `ensure_vstorage_creds` — which, if `AWS_*` is unset, exports them by parsing `access_key`/`secret_key` from `storage/terraform.tfvars` (or `TF_VAR_access_key`/`TF_VAR_secret_key` from `storage/.env`), then `ensure_remote_init` `terraform init`s the remote-backend modules. So **`deploy-all.sh` was ALWAYS creds-self-sufficient** (that's why full-stack deploys worked). The per-component `.env` AWS_* only matters when you run an INDIVIDUAL module script directly (e.g. `server/deploy-api.sh uat`), which does NOT source tfstate.sh — it only sources its own `.env`. Verified via `./deploy-all.sh uat --dry-run` (EXIT=0, 14/14 steps printed, nothing executed).

## Related
[[Running leo-customer360 deploys locally needs vStorage backend creds; CI can't do monitoring/LB]]

## The orchestrator has its OWN creds path (don't confuse the two)
`deploy-all.sh` sources `deployments/lib/tfstate.sh` and calls `ensure_vstorage_creds` — which, if `AWS_*` is unset, exports them by parsing `access_key`/`secret_key` from `storage/terraform.tfvars` (or `TF_VAR_access_key`/`TF_VAR_secret_key` from `storage/.env`), then `ensure_remote_init` `terraform init`s the remote-backend modules. So **`deploy-all.sh` was ALWAYS creds-self-sufficient** (that's why full-stack deploys worked). The per-component `.env` AWS_* only matters when you run an INDIVIDUAL module script directly (e.g. `server/deploy-api.sh uat`), which does NOT source tfstate.sh — it only sources its own `.env`. Verified via `./deploy-all.sh uat --dry-run` (EXIT=0, 14/14 steps printed, nothing executed).

## Related

- [[Running leo-customer360 deploys locally needs vStorage backend creds; CI can't do monitoring/LB]]
