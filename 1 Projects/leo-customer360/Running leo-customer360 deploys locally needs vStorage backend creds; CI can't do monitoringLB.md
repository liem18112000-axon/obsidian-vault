---
title: "Running leo-customer360 deploys locally needs vStorage backend creds; CI can't do monitoring/LB"
created: 2026-08-21
type: lesson
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, terraform, vstorage, deployment, credentials, gotcha]
---

# Running leo-customer360 deploys locally needs vStorage backend creds; CI can't do monitoring/LB

To run any **leo-customer360** deploy script locally you need TWO credential sets — and the CD pipeline deliberately can't cover the monitoring / load-balancer steps.

## Local requirements
1. **vStorage S3 backend creds** — every deploy script reads vServer IPs / DB host / redis host from Terraform OUTPUTS, whose state lives on VNG **vStorage** (S3-compatible, bucket `leocdp360-tfstate`, endpoint `https://hcm04.vstorage.vngcloud.vn`, region `us-east-1`, `workspace_key_prefix=env`). Auth is purely env: `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` (the `VSTORAGE_ACCESS_KEY`/`VSTORAGE_SECRET_KEY` CI secrets). Without them terraform errors **'No valid credential sources found'** and the deploy dies at the `terraform output servers` step (before any SSH). Set them via `export` or a `~/.aws/credentials` `[default]` profile (the S3 backend has `skip_credentials_validation=true`, so no STS needed).
2. **Local secret .env files** — `deployments/monitoring/.env` (Portainer pw, oauth2 client+cookie secrets) and `deployments/sso/.env` (`KEYCLOAK_ADMIN_PASSWORD`). Git-ignored, machine-local.
3. SSH key `~/.ssh/c360-api_ed25519`; terraform >= 1.14.8; python3.

## Why CI can't deploy monitoring or load_balancer
The CD deploy job has AWS_* (vStorage), DEPLOY_SSH_KEY, DB/REDIS passwords — and `terraform init`s only **server/postgres/cache** read-only. It runs `deploy-all.sh <env> --only <services>` and by design **NEVER** runs the infra Terraform `deploy.sh` scripts (server/storage/postgres/**load_balancer**). And **monitoring** needs the KEYCLOAK_ADMIN_PASSWORD + oauth2 `.env` secrets which are NOT in CI. So Jaeger-gate / LB-backend changes must be applied by an **operator locally** (creds #1 + #2), not by `--deploy-uat` nor `workflow_dispatch`.

## Related
[[leo-customer360 CD UAT deploys only from main + --deploy-uat marker]]
[[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]

## Related

- [[leo-customer360 CD UAT deploys only from main + --deploy-uat marker]]
- [[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]
