---
title: "VNG vServer flavor zones are per-AZ under one shared name; the flavor's flavorZoneId field IS the AZ"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [terraform, vngcloud, vserver, flavor, availability-zone, gotcha]
---

# VNG vServer flavor zones are per-AZ under one shared name; the flavor's flavorZoneId field IS the AZ

CRUCIAL: the project-level flavor-zone list (`/flavor_zones/product`) is NOT filtered by availability zone, so it shows flavor families that arent actually usable in your AZ. The console (filtered to your AZ) is the source of truth for what you can launch.

For this GreenNode account, HCM03-1C (aka "HCM3") only offers the **s-general-*** family; **s2-general-*** belongs to a DIFFERENT AZ and shows `isSoldOut: true`. (Same split as vDB: 1C = s-general, 1A = s2-general.)

## How to find the real AZ of a flavor
List a zones flavors: `GET /v1/{project}/{flavor_zone_id}/flavors`. Each flavor JSON has:
- `flavorId` (the `flav-...` uuid to pass to the server),
- `zoneId` = the flavor-ZONE uuid (what the flavor data source wants as `flavor_zone_id`),
- **`flavorZoneId` = the actual AZ string** (e.g. `HCM03-1A`/`1B`/`1C`) — confusingly named,
- `isSoldOut` (skip true), `remainingVms`, and `metaData.imageTypeSupport` (confirms e.g. "Ubuntu").

So the SAME display name (e.g. "General Purpose Code S") exists as 3 separate flavor zones, one per AZ; only the one whose flavors have `flavorZoneId == HCM03-1C` and `isSoldOut == false` is usable. The `vngcloud_vserver_flavor_zone` data source matches by name and returns the FIRST → often the wrong (sold-out) AZ. Fix: pass `flavor_zone_id` (the flavors `zoneId`) directly and skip the name lookup.

Concrete for HCM03-1C: flavor_zone_id = `9818AAB0-8DC5-4FED-898B-9EFD804AB137`, s-general-1x2 = `flav-d1e53c0d-0565-11f0-a0a4-ec2a72332f83`.

## Related
[[VNG vServer OS images are not associated with the s2-general flavor zone (image data-source trap)]]

## Related

- [[VNG vServer OS images are not associated with the s2-general flavor zone (image data-source trap)]]
