---
title: "VNG Cloud resource ID prefixes and the HCM zone_id label gotcha"
created: 2026-08-03
type: lesson
status: seedling
source: "session 2026-08-03"
tags: [vngcloud, greennode, zone, region, ids, gotcha]
---

# VNG Cloud resource ID prefixes and the HCM zone_id label gotcha

VNG Cloud / GreenNode resource IDs carry a type prefix: instance = ins-, project = pro-, network = net-, subnet = sub-, vDB instance = db-, Kafka cluster = clus-. The Terraform input vserver_project_id needs the **pro-** value, which is NOT shown on a cloud servers "General information" panel (that shows the ins- instance ID). Find pro- in the console project selector or the browser URL projectId= query param, and pick the project that owns your network/subnet.

Zone label gotcha: the console shows a friendly zone label like "HCM-1A", but the Terraform/API zone_id token is region-qualified, e.g. HCM03-1A (allowed: HCM03-1A/-1B/-1C). The console host also tells you the region (hcm-3.console.greennode.ai = HCM03). Keep zone_id + vng_vserver_base_url (hcm-3.api...) aligned to where your network/compute live, and co-locate vDB with the app. vStorage region may differ since S3 buckets are reached by endpoint, not VPC.

## Related
- [[VNG Cloud Terraform provider vDB service-to-resource mapping]]
- [[LEO Customer360 GreenNode Terraform infrastructure]]
