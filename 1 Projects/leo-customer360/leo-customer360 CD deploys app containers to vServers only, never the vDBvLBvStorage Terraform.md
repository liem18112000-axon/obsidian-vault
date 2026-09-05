---
title: "leo-customer360 CD deploys app containers to vServers only, never the vDB/vLB/vStorage Terraform"
created: 2026-08-20
type: argument
status: seedling
source: "session 2026-08-20, cd.yml"
tags: [leo-customer360, cd, terraform, safety, deployment]
---

# leo-customer360 CD deploys app containers to vServers only, never the vDB/vLB/vStorage Terraform

Decision: the `cd.yml` continuous-delivery pipeline deploys **only the vServer app containers** (customer360-api, backend-system, ads-server, frontend-admin) — pull image from GHCR + `docker run` over SSH. It must **never** run the infrastructure Terraform (`deploy.sh`) for the managed data services: `storage` (vStorage / object storage), `postgres` (vDB / managed PostgreSQL), `load_balancer` (vLB / NLB), or `server` (vServer provisioning itself).

**Why:** those are long-lived, expensive, stateful managed resources; a `terraform apply` on every deploy risks destructive drift/recreation. Provisioning is a separate, deliberate, human-run step.

**How it is enforced:** `deploy-all.sh <env> --only api,backend,ads,frontend -y`. In `deploy-all.sh`, `storage|postgres|server|load-balancer` map to `tf_step … deploy.sh` (Terraform apply) while `api|backend|ads|frontend` map to the SSH `deploy-*.sh` scripts — and `--only` restricts execution to exactly the named steps, so the tf_step ones are never reached.

**Read vs touch:** CD still runs `terraform init` + `terraform output` (READ-ONLY) on `server`/`postgres`/`cache` to resolve vServer IPs, DB host, and redis host that the app scripts need — this reads state but never `apply`s, so it does not mutate the vDB/vLB/vStorage. Context: [[Chain a CD workflow after CI with workflow_run, gating on conclusion and ref]].

## Related

- [[Chain a CD workflow after CI with workflow_run]]
- [[gating on conclusion and ref]]
