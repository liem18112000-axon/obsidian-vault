---
title: "LEO CDP environment version drift: local PG16/Redis8 vs managed PG15/Redis7"
created: 2026-08-26
type: gotcha
status: seedling
source: "leo-customer360 release-doc work, session 2026-08-26"
tags: [leo-cdp, postgres, redis, gotcha, environments]
---

# LEO CDP environment version drift: local PG16/Redis8 vs managed PG15/Redis7

In LEO Customer 360 the **local/Compose data tier versions differ from the managed UAT/PROD ones**, so version-sensitive SQL/behaviour must be validated against the *managed* versions before shipping:

- PostgreSQL: local image `postgis/postgis:16-3.5` = **PG 16**, but managed UAT/PROD = **PG 15**.
- Redis: local image `redis:8-alpine` = **8**, but managed = **Redis 7** (UAT container / PROD MemStore).

Second gotcha: **Redis listens on the non-standard port `6580`** (not 6379) throughout the stack — `REDIS_PORT=6580`, `REDIS_HOST_PORT=6580`. Point local tooling at 6580.

Why it matters: something that works on PG 16 locally can fail on PG 15 in prod; assuming 6379 makes local Redis connections silently fail.

## Related

- [[LEO CDP schema migrations are ordered plain SQL]]
- [[not dbmate or alembic]]
