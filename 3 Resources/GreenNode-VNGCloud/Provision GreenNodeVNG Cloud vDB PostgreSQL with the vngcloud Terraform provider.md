---
title: "Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider"
created: 2026-08-17
type: howto
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, terraform, postgresql, vdb]
---

# Provision GreenNode/VNG Cloud vDB PostgreSQL with the vngcloud Terraform provider

To script creating a PostgreSQL DB on GreenNode/VNG Cloud vDB (the console form at `vdb.console.greennode.ai/relational/database/create/`), use the official Terraform provider **`vngcloud/vngcloud`** rather than reverse-engineering a REST call — the console form maps 1:1 onto its resources.

**Key resources / data sources:**
- `vngcloud_vdb_relational_database` — a single (standalone) instance; set `engine_type = "PostgreSQL"`, plus `engine_version`, `db_name`, `username`, `password`, `subnet_id`, `zone_id`, `volume_size`, `public_access`, `allowed_ip_prefix`, `backup_auto/backup_time/backup_duration`, `action="start"`.
- `vngcloud_vdb_postgresql_cluster` — HA (1 writer + N readers) instead of standalone.
- `data.vngcloud_vdb_database_package` (args: engine_type, engine_version, name like `db.s-general-1x2`, zone_id) → `package_id`.
- `data.vngcloud_vdb_database_volume_type` (args: type like `Gen2-NVMe2-IOPS3000`, zone_id) → `volume_type`.

**Provider auth block:** `client_id` + `client_secret` (from vIAM service account), `token_url = https://iamapis.vngcloud.vn/accounts-api/v2/auth/token`, `vdb_base_url = https://vdb-gateway.vngcloud.vn`, `vserver_base_url`/`vlb_base_url` under `hcm-3.api.vngcloud.vn`.

**Feeding config via env / `.env`:** two paths. (1) The provider reads its OWN env vars when the argument is unset: `CLIENT_ID`, `CLIENT_SECRET`, `TOKEN_ADDRESS`, `VSERVER_BASE_URL`, `VLB_BASE_URL`, `VDB_BASE_URL`. (2) If the provider block references input variables (`client_id = var.client_id`, …), feed those with Terraform's generic `TF_VAR_<name>` env vars (`TF_VAR_client_id`, `TF_VAR_token_url`, …). Terraform has no built-in `.env` loader — source the file into the shell first (`set -a; source .env; set +a`) or use tfvars. Pick ONE mechanism per argument; a value set in the provider block wins over the provider's own env var.

**REST equivalent (for a pure curl script):** the create endpoint IS documented at `docs.api.greennode.ai/service-docs/vdb-api.html` (the VNG Cloud `docs.api.vngcloud.vn` copy 301-redirects there — another GreenNode=VNG Cloud tell). `POST https://vdb-gateway.vngcloud.vn/vdb-relational/v1/payment/database-instances` with a body of `{name, datastoreType:"PostgreSQL", datastoreVersion, packageId, volumeType, volumeSize, netIds:[subnetId], user:{name,password,databases:[{name}]}, publicAccess, backupAuto/backupDuration/backupTime, locateZoneId}`. Auth uses a Bearer token from the IAM token endpoint **plus** a `portal-user-id` header (and optional `user-type: ROOT_USER|IAM_USER`). Poll status via `GET .../vdb-relational/v1/database-instances/id/{dbInstanceId}`.

Backdrop: [[GreenNode Cloud is VNG Cloud rebranded (same IAM, gateway, Terraform provider)]].

## Related

- [[GreenNode Cloud is VNG Cloud rebranded (same IAM, gateway, Terraform provider)]]
