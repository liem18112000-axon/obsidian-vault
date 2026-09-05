---
title: "VNG vLB packages are per-AZ too: pin the ?zoneId= package uuid (full lbp- prefix) or the create rejects it"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/load_balancer"
tags: [vngcloud, vlb, load-balancer, availability-zone, gotcha]
---

# VNG vLB packages are per-AZ too: pin the ?zoneId= package uuid (full lbp- prefix) or the create rejects it

GreenNode/VNG Cloud vLB **packages are per availability-zone**, exactly like vServer flavors, images, and volume types. Creating `vngcloud_vlb_load_balancer` fails with `StatusCode: 400, {"message":"Invalid package id (...)"}` when you feed it the package uuid from the `vngcloud_vlb_lb_packages` data source, because that data source returns the DEFAULT AZ`s packages and their uuids are not valid in your zone.

Fix (same shape as the flavor/volume/image bypasses):
1. Get YOUR zone`s package uuid: `GET {vlb_base_url}/v2/{project}/loadBalancers/packages?zoneId=HCM03-1C` -> `listData[]` with per-AZ uuids. For HCM03-1C, NLB_Small = `lbp-f60d5354-0600-11f0-a0a4-ec2a72332f83` (note the `-0600-11f0-a0a4-ec2a72332f83` suffix that marks 1C, same pattern as 1C flavors/volumes). The unscoped call returns `lbp-96b6b072-...` which the create REJECTS.
2. Pass it via a direct `package_id` override (the data source can`t send `?zoneId=`).
3. Use the FULL `lbp-`-prefixed uuid — do NOT strip the prefix (the doc example shows a bare uuid but that is wrong; the create wants `lbp-...`).

So for this account, the per-AZ catalog rule is universal across vServer (flavor/image/volume) AND vLB (package): the name/default lookups resolve the disabled/default AZ; always discover the HCM03-1C uuid via `?zoneId=` and pin it directly.

## Related
[[VNG vServer default catalog endpoints return the DISABLED default AZ; use ?zoneId=<AZ> and pin UUIDs]]

## Related

- [[VNG vServer default catalog endpoints return the DISABLED default AZ; use ?zoneId=<AZ> and pin UUIDs]]
