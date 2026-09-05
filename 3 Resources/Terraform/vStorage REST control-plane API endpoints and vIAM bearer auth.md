---
title: "vStorage REST control-plane API: endpoints and vIAM bearer auth"
created: 2026-08-17
type: reference
status: seedling
source: "session 2026-08-17 (vStorage API reference)"
tags: [vngcloud, vstorage, rest-api, object-storage, viam, reference]
---

# vStorage REST control-plane API: endpoints and vIAM bearer auth

VNG Cloud vStorage has TWO planes with different hosts and auth:

- **Data plane** = the S3-compatible API at `https://<region>.vstorage.vngcloud.vn` (e.g. `hcm03`), signed with **S3 access_key/secret_key**. Buckets/objects.
- **Control plane** = a REST API at `https://<region>-api.vstorage.vngcloud.vn/api/v1` (note the `-api` in the host), authenticated with a **vIAM bearer token** — the same client_id/client_secret identity used by vDB/vServer, obtained from `https://iamapis.vngcloud.vn/accounts-api/v2/auth/token` (OAuth2 client-credentials). Projects/quota/billing/members.

Key control-plane operations (from the [vStorage API reference](https://docs.api.greennode.ai/service-docs/vstorage-api.html)):
- `POST /api/v1/projects` — create a project (createStorageProjectUsingPOST)
- `GET /api/v1/projects` — list; `GET|DELETE /api/v1/projects/{project_id}`
- `GET .../projects/{id}/quota`, `PUT .../quota` — read/update quota
- containers/objects also exist here (`/projects/{id}/containers/{container}/...`) as a REST alternative to the S3 API.

**Getting the vIAM token (gotcha — a wrong shape returns HTTP 400):** the token endpoint uses **OAuth2 client-credentials with HTTP Basic auth**, not the creds in the form body. The correct request is:

```
POST https://iamapis.vngcloud.vn/accounts-api/v2/auth/token
Authorization: Basic base64(clientId:clientSecret)
Content-Type: application/json

{"grant_type": "client_credentials"}   # scope optional
```

Response JSON has `access_token` (+ `token_type`, `expires_in`). Sending `client_id=...&client_secret=...&grant_type=...` as an x-www-form-urlencoded body (no Basic header) yields **400**. In curl: `curl -u "$ID:$SECRET" -H 'Content-Type: application/json' -d '{"grant_type":"client_credentials"}' "$TOKEN_URL"`.

**Create-project request body** (POST /api/v1/projects) — the field names are NOT the obvious ones:

```json
{
  "projectName": "leo-customer360",
  "projectType": "Gold",          // "Gold" (hot, ms access) | "Instant Archive" (cold)
  "quotaInGBytes": 1024,
  "enableAutoRenew": false,
  "isPoc": false
}
```

Required: `projectName`, `projectType`, `quotaInGBytes` (the API's own 400-ish errorMsg names these). No `region` field — the region is fixed by the host you POST to (`hcm03-api...`). Archive tier also takes `archivePeriod`.

**Gotcha:** this API returns **HTTP 200 even on failure**, with `{"success": false, "errorMsg": "...", "code": N}`. You MUST check the `success` flag, not just the HTTP status. See [[VNG vStorage API returns HTTP 200 with success-false on errors]].

Verified: base URL, POST /projects path, bearer auth, token-request shape, and the create body above (confirmed against the live API + the han02 API reference).

Related: [[vStorage project is a paid prerequisite Terraform cannot create]], [[vStorage S3 keys differ from vIAM client credentials used by vDB]], [[Manage VNG Cloud vStorage buckets with the AWS Terraform provider, not vngcloud]].

## Related

- [[vStorage project is a paid prerequisite Terraform cannot create]]
- [[vStorage S3 keys differ from vIAM client credentials used by vDB]]
