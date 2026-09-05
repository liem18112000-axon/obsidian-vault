---
title: "Derive tenant identity from the verified token, never from request input"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [authorization, idor, multi-tenancy, security]
---

# Derive tenant identity from the verified token, never from request input

Taking the tenant (or user) identity from request input, a query param, request body, or header, is an IDOR: the caller can name someone else's tenant. This holds even when it is "only for dev mode", because dev defaults ship.

Always derive the security principal (tenant_id, user_id, roles) from the verified token/session. If an endpoint also accepts a tenant argument (e.g. for filtering), compare it to the token's tenant and reject mismatches (403). Never pass a request-supplied tenant into the RLS session variable.

## Related

- [[Postgres RLS should be defense-in-depth]]
- [[not the sole tenant boundary]]
