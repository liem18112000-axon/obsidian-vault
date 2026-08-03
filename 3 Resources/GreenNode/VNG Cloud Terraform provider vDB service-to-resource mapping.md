---
title: "VNG Cloud Terraform provider vDB service-to-resource mapping"
created: 2026-08-03
type: howto
status: seedling
source: "session 2026-08-03"
tags: [terraform, vngcloud, vdb, postgresql, redis, kafka]
---

# VNG Cloud Terraform provider vDB service-to-resource mapping

The official Terraform provider is **`vngcloud/vngcloud`** (Terraform Registry; latest v1.3.18 as of 2026-08). Service → resource mapping:

- **PostgreSQL** — `vngcloud_vdb_postgresql_cluster` (HA, 2–10 nodes) *or* `vngcloud_vdb_relational_database` (standalone; set `engine_type = "PostgreSQL"`).
- **Redis** — `vngcloud_vdb_memstore_database` (`engine_type = "Redis"`).
- **Kafka** — `vngcloud_vdb_kafka_cluster` + `vngcloud_vdb_kafka_topic` + `vngcloud_vdb_kafka_user`.
- Each family has a `*_config_group` resource for engine parameters.

**Provider auth block:** `token_url` (https://iamapis.vngcloud.vn/accounts-api/v2/auth/token), `client_id`, `client_secret`, `vdb_base_url` (https://vdb-gateway.vngcloud.vn), `vserver_base_url`, `vlb_base_url`.

**Gotcha:** package (flavor) and volume-type IDs are not hard-coded — resolve them with data sources (`vngcloud_vdb_database_package`, `vngcloud_vdb_postgresql_cluster_package`, `vngcloud_vdb_kafka_package`, `*_volume_type`) by **name + engine_type + engine_version + zone**. A wrong name/version fails at `plan` time on the data-source lookup, so confirm exact package names in the vDB console first. Standalone packages use `db.*` names, clusters use `vdb.*`, Kafka uses `db-kafka.*`.

## Related
- [[GreenNode is VNG Cloud's AI cloud exposing vDB and vStorage managed services]]
- [[vStorage has no Terraform resource so manage buckets via the AWS S3 provider]]
- [[LEO Customer360 GreenNode Terraform infrastructure]]
