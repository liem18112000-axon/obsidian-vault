---
title: "Luz 403 Not allowed means the token has no tenant claim, not a missing permission"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 (luz_docs_import k6 403 investigation)"
tags: [luz, auth, jwt, gotcha, 403]
---

# Luz 403 Not allowed means the token has no tenant claim, not a missing permission

A Luz REST endpoint guarded by `@PermissionAllowed(...)` runs `TokenAuthenticationRequestFilter` first, and that filter returns **HTTP 403** with body `{"code":"403","message":"Not allowed"}` for any bearer token whose **company / person / individual tenant claim is null** — *before* it ever checks the permission. So "Not allowed" is usually a **tenant-binding** failure, not a missing-role failure: a token can carry the correct permission/role (even `all_tenants_access`) and still 403 purely because it isn't bound to a tenant. The signature is fine (the service still fetches `jwt-service/public-keys/active` to validate it) — only the tenant claims are absent.

This is the trap behind Luz's two token endpoints:

- `POST /luzsec/api/tokens` (Basic `admin:admin`) → a **tenant-less** technical token. Claims are only `sub, iss, user_roles, exp, iat`. No `company-tenant`, `person-tenant`, or `tenantId`. → rejected by the filter.
- `POST /luzsec/api/{tenantId}/access/tokens?type=all-tenant` → an **all-tenant** token that also carries `company-tenant` + `person-tenant` + `tenantId` (+ `security_classes`). → accepted. This is what `luz-skill-get-token` wraps.

Practical rules:
- To act on a tenant-scoped endpoint, always mint via the `/access/tokens` endpoint, not the bare `/tokens` one.
- You can mint `type=all-tenant` under **any real company tenant** — including the target tenant itself. The `all_tenants_access` role then lets that single token operate across *all* tenants.
- Minting under a **non-company** tenant id (e.g. a person-tenant uuid) returns **404** — the `{tenantId}` in the path must resolve to a company tenant.

Verified live on dev 2026-08-18: the bare-`/tokens` token 403'd on `upload-zip`; the `/access/tokens?type=all-tenant` token (minted under the target tenant) got HTTP 200.

## Related

- [[luz-skill-get-token]]
