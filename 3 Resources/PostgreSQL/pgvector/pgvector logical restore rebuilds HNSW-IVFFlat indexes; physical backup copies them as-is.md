---
title: "pgvector logical restore rebuilds HNSW-IVFFlat indexes; physical backup copies them as-is"
created: 2026-08-16
type: lesson
status: seedling
source: "session 2026-08-16 postgres/docs research"
tags: [pgvector, postgres, backup, restore, gotcha]
---

# pgvector logical restore rebuilds HNSW-IVFFlat indexes; physical backup copies them as-is

A **logical** `pg_dump` stores vector column *data* fine, but does **not** store HNSW/IVFFlat index contents — it emits the `CREATE INDEX ... USING hnsw/ivfflat` DDL and the index is **rebuilt from scratch on restore**. For a large embedding table the rebuild can take far longer than loading the rows, so it dominates RTO.

Cut the rebuild cost by raising, for the restore session, `maintenance_work_mem` (build is much faster when the graph fits in it) and `max_parallel_maintenance_workers` (parallel HNSW builds since pgvector 0.6.0). In containers, `--shm-size` / `/dev/shm` must be **≥ maintenance_work_mem** or the parallel build errors out. IVFFlat must be built **after** data load (it clusters existing rows); HNSW has no such dependency — `pg_dump` ordering (data then indexes) is already correct.

A **physical** backup (pg_basebackup / pgBackRest / base+WAL / PITR) copies the index files **as-is** — no rebuild — so it is the better track for large vector tables. Either way the restore target must have the pgvector binary present at a version ≥ source.

Related: [[postgis-postgis 16-3.5 image does not bundle pgvector]], [[VNG vDB PostgreSQL cluster has no Terraform backup args (standalone-only)]].

## Related

- [[postgis-postgis 16-3.5 image does not bundle pgvector]]
