---
title: "VNG Cloud vServer Terraform: catalog ids resolve via a zone-UUID lookup chain"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [terraform, vngcloud, greennode, vserver, gotcha]
---

# VNG Cloud vServer Terraform: catalog ids resolve via a zone-UUID lookup chain

To provision a VNG Cloud / GreenNode VM with `vngcloud_vserver_server`, you cannot pass human names for the flavor, image, or disk type — the resource wants opaque ids (`flavor_id`, `image_id`, `root_disk_type_id`). Resolving those by name takes a **two-hop lookup**, because each catalog data source needs a *zone UUID* that is itself looked up by display name.

## The resolution chain

```
zone display name ─► *_zone data source ─► zone UUID ─► catalog data source (by name) ─► id ─► server
```

- `vngcloud_vserver_flavor_zone` (name e.g. `"General v2 Instances"`) → `id` → feed as `flavor_zone_id` to `vngcloud_vserver_flavor` (name e.g. `s2-general-4x8`) and `vngcloud_vserver_image` (name e.g. `Ubuntu 24.04 x64`).
- `vngcloud_vserver_volume_type_zone` (name e.g. `"SSD"`) → `id` → feed as `volume_type_zone_id` to `vngcloud_vserver_volume_type` (name e.g. `SSD-IOPS3000`).

The critical gotcha: `flavor_zone_id` / `volume_type_zone_id` are **UUIDs**, NOT the `HCM03-1A`-style `zone_id`. The `zone_id` argument still exists separately on the server resource.

## Why it bites

Catalog + zone names are **account- and zone-specific** — copy them verbatim from the console "Create instance" form. A wrong name makes the data source return an **empty id** (not an error), which then surfaces downstream as a confusing `Missing required argument` on `flavor_id`/`image_id`/`root_disk_type_id`. Guard each with a `lifecycle { precondition { condition = try(data...id, "") != "" ... } }` so the plan fails early with an actionable message.

## Other required fields

`vngcloud_vserver_server` requires BOTH `network_id` AND `subnet_id` (not just a subnet), plus `encryption_volume` (bool). Flavor names for the s2-general family: `s2-general-4x8` (4 vCPU / 8 GB), `s2-general-8x16` (8 vCPU / 16 GB).

Context: built in `leo-customer360/deployments/server`, mirroring the `deployments/postgres` layout — per-env `overlays/<env>.tfvars`, Terraform workspaces for state isolation, and a `deploy.sh <uat|prod> plan|apply|destroy` wrapper. Same empty-id-on-no-match gotcha exists for the vDB package/volume data sources in the postgres deployment.

## Related
[[GreenNode vDB PostgreSQL Terraform]]

## Related

- [[GreenNode vDB PostgreSQL Terraform]]
