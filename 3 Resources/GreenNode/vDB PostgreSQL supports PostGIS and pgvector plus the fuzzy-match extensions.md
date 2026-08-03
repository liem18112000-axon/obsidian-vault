---
title: "vDB PostgreSQL supports PostGIS and pgvector plus the fuzzy-match extensions"
created: 2026-08-03
type: observation
status: seedling
source: "session 2026-08-03"
tags: [vngcloud, postgresql, postgis, pgvector, extensions]
---

# vDB PostgreSQL supports PostGIS and pgvector plus the fuzzy-match extensions

VNG Cloud **vDB PostgreSQL supports the full extension set** a PostGIS+pgvector CDP needs, so a managed instance is a viable drop-in for a self-hosted `postgis/postgis` image:

- `postgis` 3.6.3 (+ raster / tiger_geocoder / topology)
- `vector` (pgvector) 0.8.2
- `pg_trgm`, `fuzzystrmatch` (fuzzy identity matching)
- `uuid-ossp`, `pgcrypto`, `btree_gin`
- also available: `pg_partman`, `pg_repack`, `pgaudit`, `timescaledb`

**Default-enabled differs by topology:** on a **standalone** instance many (postgis, vector, pg_trgm) are enabled by default; on a **cluster** most must be enabled manually with `CREATE EXTENSION`. Either way, run an idempotent `CREATE EXTENSION IF NOT EXISTS ...` script post-provision — the Terraform provider does not manage in-database objects.

## Related
- [[Postgres Row-Level Security is bypassed by superusers so the app needs a non-superuser role]]
- [[VNG Cloud Terraform provider vDB service-to-resource mapping]]
- [[LEO Customer360 GreenNode Terraform infrastructure]]
