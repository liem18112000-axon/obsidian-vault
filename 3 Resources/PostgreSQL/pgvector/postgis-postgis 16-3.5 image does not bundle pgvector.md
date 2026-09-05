---
title: "postgis-postgis 16-3.5 image does not bundle pgvector"
created: 2026-08-16
type: gotcha
status: seedling
source: "session 2026-08-16 postgres/docs research"
tags: [pgvector, postgis, docker, postgres, gotcha]
---

# postgis-postgis 16-3.5 image does not bundle pgvector

The official `postgis/postgis:16-3.5` Docker image ships PostGIS but **not** pgvector. To get the `vector` extension you must add it — e.g. `apt-get install postgresql-16-pgvector` from the PGDG repo (the image is Debian/PGDG-compatible) in a derived Dockerfile, then `CREATE EXTENSION vector;`.

**Backup/restore consequence:** any restore target — logical restore, physical restore, a streaming replica, or a logical-replication subscriber — must run an image that carries the pgvector binary at a version **≥** the source, or `CREATE EXTENSION vector` fails with `could not open extension control file ".../vector.control"` and the whole restore aborts. (The SQL name is `vector`, not `pgvector`.) In LEO CDP Customer360 the derived image is `customer360-postgres:local` built from `postgres/Dockerfile`; never restore into stock `postgis/postgis:16-3.5`.

Related: [[pgvector logical restore rebuilds HNSW-IVFFlat indexes; physical backup copies them as-is]].

## Related

- [[pgvector logical restore rebuilds HNSW-IVFFlat indexes; physical backup copies them as-is]]
