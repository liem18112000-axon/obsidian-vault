---
title: "VNG vServer flavor resize (s-general-1x2 -> 2x4) is an in-place terraform change (0 destroy) but reboots the box"
created: 2026-08-23
type: lesson
tags: [vngcloud, terraform, resize, flavor, leo-customer360]
---

# VNG vServer flavor resize (s-general-1x2 -> 2x4) is an in-place terraform change (0 destroy) but reboots the box

Resizing a VNG vServer to a bigger flavor (e.g. s-general-1x2 [1vCPU/2GB] -> s-general-2x4 [2vCPU/4GB]) via the vngcloud terraform provider is an **in-place update, NOT a replacement**: a plan changing the api box's flavor_name showed `vngcloud_vserver_server.this["api"] will be updated in-place / ~ flavor_id = ... -> ... / Plan: 0 to add, 2 to change, 0 to destroy`. 0 destroy = instance + disk + containers preserved (no rebuild, no data loss).

**But a flavor change = stop -> resize -> start**, i.e. a few minutes of downtime for EVERYTHING on that box (its containers auto-restart via --restart unless-stopped). Do it in a maintenance window. And it **costs more** (higher tier ~ 2x the compute rate) — check VNG console pricing first.

**Availability check:** run `deployments/server/discover-catalog.py <env>` (reads TF_VAR_client_id/secret from .env or terraform.tfvars) — it lists flavor zones + their flavors. The api box pins flavor_zone_id 9818AAB0-... ("General Purpose Code S" @ HCM03-1C), which offers s-general-{1x2,2x4,4x8,8x16,16x32}. The flavor is resolved name->id via a data source in the zone, so any listed flavor in that zone just works.

**How:** set the server key's `flavor_name` in server/overlays/<env>.tfvars, then `./deploy.sh <env> apply`. Use `TARGET='vngcloud_vserver_server.this["api"]' ./deploy.sh <env> apply` to resize ONLY that box (avoids sweeping unrelated drift on the other boxes).

**Gotchas hit while probing:** running `terraform plan` DIRECTLY (not via deploy.sh) fails with "No valid credential sources found / EC2 IMDS" because the S3-compatible state BACKEND needs AWS_* (VSTORAGE) creds that deploy.sh loads by sourcing .env — `set -a; . ./.env; set +a` before terraform. To test replace-vs-in-place safely without touching the real overlay, sed a temp copy and `terraform plan -var-file=temp` (plan is read-only), then delete it.

Source: leo-customer360 uat api box resize check, 2026-08-23.
