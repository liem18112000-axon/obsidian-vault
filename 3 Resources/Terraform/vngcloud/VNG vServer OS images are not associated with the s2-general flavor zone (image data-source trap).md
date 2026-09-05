---
title: "VNG vServer: OS images are not associated with the s2-general flavor zone (image data-source trap)"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [terraform, vngcloud, vserver, image, flavor-zone, gotcha]
---

# VNG vServer: OS images are not associated with the s2-general flavor zone (image data-source trap)

Two traps hit when resolving VNG Cloud vServer catalog names for a plain Ubuntu VM (via the `vngcloud_vserver_*` data sources), found by enumerating the real catalog with discover-catalog.py:

## 1. Duplicate flavor-zone NAMES → first-match wins
`data.vngcloud_vserver_flavor_zone` matches by display name and returns the FIRST zone with that name. This account has MANY zones literally named `"General Purpose Code S"` with different UUIDs — the first one only holds `eci.ins.s-general-*` (elastic-container) flavors, so selecting `flavor_zone_name = "General Purpose Code S"` + flavor `s-general-1x2` fails ("flavor not found") even though a LATER same-named zone does contain `s-general-1x2`. Fix: use the `s2-general-*` family whose zone is uniquely named **"General Purpose"** (contains s2-general-1x2/2x4/4x8/8x16). Rule of thumb: prefer a UNIQUELY-named flavor zone.

## 2. OS images are NOT associated with the s2-general flavor zone → bypass the image data source
`data.vngcloud_vserver_image` only returns an image if the resolved `flavor_zone_id` is in that image`s `flavorZoneIds`. The Ubuntu images (`1_Ubuntu-24.04x64`, etc. — matched on `imageVersion`) list only the v1 zones (HQSoft/IOTLink/EasyZone + some UUIDs), NOT the "General Purpose" (s2-general) zone. So name-based image lookup fails for s2-general. Fix: pass the image id (`img-...`) DIRECTLY to the server resource and skip the data source (`count = var.image_id == "" ? 1 : 0`); the `vngcloud_vserver_server` RESOURCE only needs a valid image_id + flavor_id and does not enforce the association.

## 3. Naming specifics (this account, HCM03-1C)
- SSD root-disk types are named by IOPS number: `"3000"` (base), `"3200"`, `"6400"`, `"10000"` under volume_type_zone `"SSD"` — NOT `SSD-IOPS3000`.
- Cheapest general flavor: `s2-general-1x2` (1 vCPU / 2 GB).

## Related
[[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]
[[VNG Cloud vServer discovering account catalog names via the vserver-gateway API]]

## Related

- [[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]
- [[VNG Cloud vServer discovering account catalog names via the vserver-gateway API]]
