---
title: "Recovering an orphaned VNG vLB resource after a Terraform state-save failure"
created: 2026-08-21
type: lesson
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, terraform, vngcloud, vlb, state-recovery, import, gotcha]
---

# Recovering an orphaned VNG vLB resource after a Terraform state-save failure

How to recover when a Terraform apply against the VNG/GreenNode vLB **creates resources but fails to persist state** (leocdp360 load_balancer module, S3/vStorage backend).

## The failure
A transient DNS blip on the vStorage endpoint (`lookup hcm04.vstorage.vngcloud.vn: no such host`) during the state UPLOAD after resources were created -> provider 'crashed' (`Plugin did not respond`). Real VNG resources exist but aren't in state -> drift. A blind retry fails with **`Duplicated Pool name`** (create-again conflict).

## Recovery steps
1. Terraform writes **`errored.tfstate`** in the module dir on a backend-save failure — it holds the resources whose creation COMPLETED. Inspect it (`python -c` over the json) to see what got recorded.
2. When the backend is reachable again: `terraform state push errored.tfstate` — reconciles the recorded resources (here: the secgroup rule) so they aren't recreated. (Serial must be > remote; it is, being post-apply.)
3. Anything created but NOT in errored.tfstate (creation was mid-flight at crash) is a true orphan -> **import it**.
4. **Getting the orphan's id:** the service account can CREATE vLB resources but is **IAM-denied on LIST/GET** (`IAM_PERMISSION_DENIED`), so you can't enumerate via API — the operator reads the id (`pool-...`) from the **GreenNode console** (LB detail page).
5. **Import format for a vLB pool: `{projectId}:{loadBalancerId}:{poolId}`** (the provider error tells you). E.g. `terraform import -var-file=overlays/uat.tfvars 'vngcloud_vlb_pool.this["jaeger"]' pro-...:lb-...:pool-...`. Pass the `-var-file` so the for_each key exists in config.
6. `./deploy.sh <env> apply` — plan is now just the remaining resource (the listener); applies clean.

## Gotcha
The VNG token endpoint (`iamapis.vngcloud.vn/accounts-api/v2/auth/token`) wants `grant_type=client_credentials` with the client id/secret as **HTTP Basic auth** (not in the body). But even with a valid token, this service account's IAM policy blocks vLB reads — so API enumeration is a dead end; use the console for ids.

## Related
[[Running leo-customer360 deploys locally needs vStorage backend creds; CI can't do monitoring/LB]]

## Related

- [[Running leo-customer360 deploys locally needs vStorage backend creds; CI can't do monitoring/LB]]
