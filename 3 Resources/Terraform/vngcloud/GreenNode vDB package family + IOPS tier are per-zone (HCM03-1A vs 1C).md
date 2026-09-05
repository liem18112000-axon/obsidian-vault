---
title: "GreenNode vDB package family + IOPS tier are per-zone (HCM03-1A vs 1C)"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/postgres"
tags: [terraform, vngcloud, greennode, vdb, postgres, zone, gotcha]
---

# GreenNode vDB package family + IOPS tier are per-zone (HCM03-1A vs 1C)

On this GreenNode/VNG Cloud account, the vDB (managed PostgreSQL) catalog names are **per availability zone** — switching an overlay from one AZ to another means changing the compute package family AND the volume type together, not just `zone_id`.

Observed working values:
- **HCM03-1A**: `package_name = db.s2-general-8x16` (family `db.s2-general-*`), `volume_type = Gen2-NVMe2-IOPS10000`.
- **HCM03-1C**: `package_name = db.s-general-8x16` (family `db.s-general-*` — note NO `s2`), `volume_type = ssd-iops3200-HCM03-1C`. 10000 IOPS is NOT offered for standalone in 1C (maxes at 3200); reaching 10000 needs 1A or the cluster topology.

Also for THIS account only **HCM03-1C** is enabled for vServer/subnets (1A/1B are "contact to enable"). So a from-scratch deploy generally targets 1C, which forces the `db.s-general-*` family.

Consequence: setting `zone_id` to 1C while leaving a 1A-only `package_name`/`volume_type` fails the plan (the package/volume data source errors "not found"). When moving `deployments/postgres/overlays/uat.tfvars` to 1C, change all three: `zone_id`, `package_name` (s-general family), `volume_type` (ssd-iops3200-HCM03-1C).

Extra gotcha: changing `zone_id` on an already-deployed instance is a REPLACE (destroy+recreate in the new AZ), not an in-place move — data is lost unless backed up.

## Related
[[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]

## Related

- [[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]
