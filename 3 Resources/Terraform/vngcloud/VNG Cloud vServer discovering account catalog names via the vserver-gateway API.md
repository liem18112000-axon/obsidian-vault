---
title: "VNG Cloud vServer: discovering account catalog names via the vserver-gateway API"
created: 2026-08-18
type: howto
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/server"
tags: [terraform, vngcloud, greennode, vserver, api, oauth2, gotcha]
---

# VNG Cloud vServer: discovering account catalog names via the vserver-gateway API

When Terraform `vngcloud_vserver_*` data sources fail with `not found ... with name X`, the fix is to discover the real per-account catalog names via the vserver-gateway REST API. All GET, base = `vserver_base_url` (default `https://hcm-3.api.vngcloud.vn/vserver/vserver-gateway`).

## Auth (the non-obvious part)
Token endpoint `https://iamapis.vngcloud.vn/accounts-api/v2/auth/token` is OAuth2 **client-credentials**, and the SDK (`golang.org/x/oauth2/clientcredentials`) sends the client id/secret via **HTTP Basic auth header** with body `grant_type=client_credentials&scope=email`. Gotchas found empirically:
- client_id/secret in the *body* → `AUTHENTICATION_FAILED` (must be Basic header).
- `scope` is required and must be non-empty; the SDK uses `scope=email`.
Response has `access_token`; then call APIs with `Authorization: Bearer <token>`.

## List endpoints (path uses project id `pro-...`)
- Flavor zones:  `GET /v1/{project}/flavor_zones/product` → `{flavorZones:[{name,id}]}`
- Flavors in a zone: `GET /v1/{project}/{flavor_zone_id}/flavors` → `{flavors:[{name,...}]}`
- Volume-type zones: `GET /v1/{project}/volume_type_zones` → `{volumeTypeZones:[{name,id}]}`
- Volume types in a zone: `GET /v1/{project}/{volume_type_zone_id}/volume_types` → `{volumeTypes:[{name,iops,minSize,maxSize}]}`
- OS images: `GET /v1/{project}/images/os` → `{images:[{imageVersion,imageType,id,flavorZoneIds}]}`

## Which JSON field each Terraform input matches
- `flavor_zone_name`      ⇒ flavorZones[].name
- flavor (`servers[].flavor_name`) ⇒ flavors[].name (listed under a flavor zone)
- `volume_type_zone_name` ⇒ volumeTypeZones[].name
- `root_disk_type_name`   ⇒ volumeTypes[].name (under a volume-type zone)
- `image_name`            ⇒ images[].**imageVersion** (NOT a `name` field), AND the image is only valid if its `flavorZoneIds` contains the chosen flavor zone id.

## Why it matters
These data sources ERROR on a name miss (not empty id), so `try(...id,"")` preconditions cannot catch them — the names must be exact. Catalog names are per-account/zone, so hardcoded guesses (`General v2 Instances`, `SSD-IOPS3000`) fail. A read-only discovery script (`deployments/server/discover-catalog.py`) enumerates all of the above.

## Related
[[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]

## Related

- [[VNG Cloud vServer Terraform catalog ids resolve via a zone-UUID lookup chain]]
