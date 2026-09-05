---
title: "Remote Terraform state needs no manual sync — bake creds + init into the deploy orchestrator to guarantee alignment"
created: 2026-08-21
type: lesson
status: seedling
source: "session 2026-08-21, deployments/lib/tfstate.sh"
tags: [terraform, remote-state, ci-cd, operations, leo-customer360]
---

# Remote Terraform state needs no manual sync — bake creds + init into the deploy orchestrator to guarantee alignment

With a **remote** Terraform backend you never "sync state down" — Terraform reads/writes it live from the bucket on every command, so local and CI runs operate on the same state by construction. What a machine actually needs is only: (1) `terraform init` ONCE per module per checkout (binds `.terraform/` to the remote backend; `.terraform/` is gitignored and machine-local; this is NOT `-migrate-state`, which is a one-time migration), and (2) the backend credentials in the environment (e.g. `AWS_ACCESS_KEY_ID`/`AWS_SECRET_ACCESS_KEY`).

Gotcha: switching from a local to a remote backend means commands that previously needed NO creds (`terraform output`, `workspace select`) now must authenticate — scripts that read outputs silently break locally until creds are exported.

Fix to make it foolproof: have the deploy orchestrators preflight (a) load the backend creds into the env from existing config and (b) run `terraform init -input=false` on the remote-backend modules — both idempotent. Then a local `./deploy-all.sh` can never drift onto a stale local `terraform.tfstate.d/` copy. In leo-customer360 this is `deployments/lib/tfstate.sh` (`ensure_vstorage_creds` + `ensure_remote_init`) sourced by `deploy-all.sh`. Also: the old local `terraform.tfstate.d/` files become stale/ignored after migration, and vStorage has no lock table so there is no state locking (avoid concurrent applies). Related: [[Terraform S3 remote backend for VNG vStorage (S3-compatible) config recipe]].

## Related

- [[Terraform S3 remote backend for VNG vStorage (S3-compatible) config recipe]]
