---
title: "VNG Cloud list-projects endpoint is GET vserver-gateway/v1/projects (not accounts-api)"
created: 2026-08-17
type: reference
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, api, project, iam]
---

# VNG Cloud list-projects endpoint is GET vserver-gateway/v1/projects (not accounts-api)

To get your VNG Cloud/GreenNode **project id** (`pro-...`) via API (not just the console): authenticate at `https://iamapis.vngcloud.vn/accounts-api/v2/auth/token` (OAuth2 client_credentials, Basic auth) for a Bearer token, then:

`GET https://hcm-3.api.vngcloud.vn/vserver/vserver-gateway/v1/projects`  (header `Authorization: Bearer <token>`)

returns the account's projects; walk the JSON for values matching `^pro-`.

**Endpoints that do NOT work (tested):** `accounts-api/v2/projects` -> 403; `vstorage-gateway/v1/projects` -> 404; `vserver-gateway/**v2**/projects` -> 404. The IAM Accounts API has NO project-list endpoint (only `/v1/auth/userinfo` = userId/accountId, no project). So the **v1 vserver-gateway** path is the one that works.

Why it matters: `vngcloud_vserver_network`/`_subnet` Terraform resources REQUIRE `project_id`, but the console/docs bury it. See [[Terraform optional-resource toggle: create-or-reuse via count + a local that picks the id]] and [[GreenNode Cloud is VNG Cloud rebranded (same IAM, gateway, Terraform provider)]].

## Related

- [[Terraform optional-resource toggle: create-or-reuse via count + a local that picks the id]]
