---
title: "VNG vServer default catalog endpoints return the DISABLED default AZ; use ?zoneId=<AZ> and pin UUIDs"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [vngcloud, vserver, availability-zone, volume-type, gotcha]
---

# VNG vServer default catalog endpoints return the DISABLED default AZ; use ?zoneId=<AZ> and pin UUIDs

The single biggest GreenNode/VNG Cloud vServer AZ trap: the default catalog list endpoints return the DEFAULT availability zone`s entries, and on this account the default AZ (HCM03-1A) is **DISABLED** ("contact to enable"). So `vngcloud_vserver_volume_type_zone` (name lookup) returns the HCM03-1A "SSD"/"NVME" zones, and creating a server in HCM03-1C then fails: `{"message":"This volume type dont support zone with ID :HCM03-1C"}`.

Key facts:
- The volume_type_zone JSON has a `zone` object: `{uuid: HCM03-1A, isEnabled: false, description: "... Contact to enable."}` — thats how you see which AZ a zone belongs to and whether its enabled. The zones `description` is the exact string the console shows (e.g. SSD = "IOPS: 200-20000, Throughtput: 200MB/s - 400MB/s, Size 10GB-5TB, Root Disk Size: 20GB-2TB").
- The REAL per-AZ zone is reachable by adding a query param: `GET /v1/{project}/volume_type_zones?zoneId=HCM03-1C` returns the enabled HCM03-1C SSD zone (id C0A35725-…) whose volume types have their own vtype-… ids (SSD 3000 = vtype-e782f8e1-0569-11f0-a0a4-ec2a72332f83).
- The Terraform data source cannot send `?zoneId=`, so bypass it: pass a direct `root_disk_type_id` (vtype-…). Same pattern as flavor_zone_id / image_id overrides.

General rule for this provider: ALL the name→id data sources (flavor zone, volume type zone, image) silently resolve against the DEFAULT (possibly disabled) AZ and cant target your AZ; on a single-AZ-enabled account, reference everything by UUID via override variables discovered from the API with the AZ-aware query/fields.

## Related
[[VNG vServer apply-time gotchas: password policy (* @ !) and AZ-restricted volume types (1C needs NVME)]]
[[VNG vServer flavor zones are per-AZ under one shared name; the flavor s flavorZoneId field IS the AZ]]

## Related

- [[VNG vServer flavor zones are per-AZ under one shared name; the flavor s flavorZoneId field IS the AZ]]
