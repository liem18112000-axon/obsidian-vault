---
title: "10000-IOPS standalone vDB PostgreSQL needs a vServer-enabled zone that offers Gen2-NVMe2-IOPS10000 (HCM03-1A)"
created: 2026-08-17
type: observation
status: seedling
source: "session 2026-08-17"
tags: [greennode, vngcloud, vdb, iops, zone, decision]
---

# 10000-IOPS standalone vDB PostgreSQL needs a vServer-enabled zone that offers Gen2-NVMe2-IOPS10000 (HCM03-1A)

Decision (leo-customer360 postgres deploy): to run a **10000-IOPS single-node** PostgreSQL on GreenNode/VNG Cloud vDB, the instance's subnet must live in a **vServer-ENABLED zone that ALSO offers the Gen2-NVMe2-IOPS10000 standalone volume**.

The bind for this account:
- vServer subnets can only be created in **HCM03-1C** (1A/1B disabled).
- HCM03-1C **standalone** volumes cap at **3200 IOPS** (`ssd-iops{200..3200}-HCM03-1C`). 10000 IOPS in 1C exists only for the **cluster** topology (`SSD-IOPS10000`).
- HCM03-1A **standalone** DOES offer `Gen2-NVMe2-IOPS10000` (+ package `db.s2-general-8x16`), but vServer is disabled there, so the subnet can't be created.

So single-node 10000 IOPS is impossible until **HCM03-1A is enabled for vServer** (GreenNode support ticket for project pro-8986f5c6...). Chosen path: keep the standalone config pinned to HCM03-1A (db.s2-general-8x16 + Gen2-NVMe2-IOPS10000) and treat apply as BLOCKED until support enables the AZ. Alternatives considered & rejected: vDB cluster in 1C (HA, higher cost, Backup-Center backups) and standalone@3200 in 1C. See [[Recover undocumented vDB API endpoints by grep-ing the vngcloud provider binary]] and [[GreenNode vDB create constraints: instance name 6-20 chars, password start-with-letter, package family s2-general]].

## Related

- [[Recover undocumented vDB API endpoints by grep-ing the vngcloud provider binary]]
