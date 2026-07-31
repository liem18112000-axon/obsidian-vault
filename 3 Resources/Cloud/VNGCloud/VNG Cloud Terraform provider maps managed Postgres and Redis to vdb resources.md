---
title: "VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources"
created: 2026-07-26
type: howto
status: seedling
source: "session 2026-07-26"
tags: [vngcloud, terraform, postgres, redis]
---

# VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources

The `vngcloud/vngcloud` Terraform provider authenticates with client_id + client_secret (plus token_url and per-service base URLs: vserver_base_url, vlb_base_url, vdb_base_url; defaults target region HCM03).

Key resource names:
- Managed PostgreSQL / MySQL (RDS): `vngcloud_vdb_relational_database` (engine_type = "PostgreSQL"). Needs package_id + volume_type from the `vngcloud_vdb_database_package` / `vngcloud_vdb_database_volume_type` data sources.
- Managed Redis (MemoryStore): `vngcloud_vdb_memstore_database` (engine_type = "Redis"); volume is computed, not an input.
- Compute: `vngcloud_vserver_server`; network `vngcloud_vserver_network` + `_subnet` + `_secgroup` + `_secgrouprule`; `vngcloud_vserver_sshkey`.

Gotcha: the `vngcloud_vserver_flavor` and `_image` data sources require a `flavor_zone_id`, obtained from the `vngcloud_vserver_flavor_zone` data source (by display name). Same pattern for volume types via `vngcloud_vserver_volume_type_zone`. So you look resources up by human name, and the provider resolves the UUIDs.

Managed DBs take `public_access` + `allowed_ip_prefix`; set public_access=false and allowed_ip_prefix=[subnet_cidr] to keep them private to the VPC.

## Related

- [[GreenNode cloud runs on VNG Cloud infrastructure]]
