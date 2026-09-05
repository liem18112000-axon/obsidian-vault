---
title: "Response caches must include the authenticated tenant in the key"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [caching, multi-tenancy, security, redis, gotcha]
---

# Response caches must include the authenticated tenant in the key

A response cache keyed only on request parameters sits *in front of* the database, so a cache HIT skips the DB (and therefore skips RLS) entirely. If the authenticated tenant is not part of the key, tenant A populates an entry and tenant B's identical request reads A's data.

Rule: fold the auth context (tenant id, and user id where results are user-scoped) into every cache key for tenant-scoped routes, or do not cache those routes. The tenant usually lives on the request/session context, not in the handler's kwargs, so a generic "cache by primitive kwargs" decorator will miss it.

## Related

- [[Postgres RLS should be defense-in-depth]]
- [[not the sole tenant boundary]]
