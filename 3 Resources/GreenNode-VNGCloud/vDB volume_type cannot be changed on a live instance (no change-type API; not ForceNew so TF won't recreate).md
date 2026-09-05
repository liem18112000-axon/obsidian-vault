---
title: "vDB volume_type cannot be changed on a live instance (no change-type API; not ForceNew so TF won't recreate)"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18"
tags: [greennode, vngcloud, vdb, terraform, volume, gotcha]
---

# vDB volume_type cannot be changed on a live instance (no change-type API; not ForceNew so TF won't recreate)

GreenNode/VNG Cloud vDB standalone (`vngcloud_vdb_relational_database`): the **volume TYPE (IOPS tier) cannot be changed on an existing instance**. Corroborated two ways:
- Provider binary exposes only `.../{instanceId}/resize-storage` (volume SIZE up) and `.../{instanceId}/resize-instance` (flavor/package) — **no change-volume-type endpoint**.
- In the TF schema `volume_type` is **NOT ForceNew** (only engine_*, name, subnet_id, username, db_name, zone_id, backup_id, is_poc are). So Terraform won't destroy+recreate to change it either — it attempts an unsupported in-place update -> apply error / no-op / drift.

**Gotcha:** because it's not ForceNew, `terraform plan` may show `~ volume_type update in-place` for a type change that apply cannot actually perform — don't trust that plan line for volume_type.

**To change volume type:** recreate the instance (destroy+create) or restore a backup into a new instance with the desired type. `volume_size` (grow-only) and the flavor/package CAN be changed in place.

Zone reality: HCM03-1C standalone volume types are `ssd-iops200..3200` (max 3200 IOPS); higher IOPS is cluster-only. See [[GreenNode vDB create constraints: instance name 6-20 chars, password start-with-letter, package family s2-general]].

## Related

- [[GreenNode vDB create constraints: instance name 6-20 chars]]
- [[password start-with-letter]]
- [[package family s2-general]]
