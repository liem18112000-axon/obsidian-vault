---
title: "Running post-deploy SQL against a managed vDB (psql \gexec, dockerized client, private-IP caveat)"
created: 2026-08-18
type: howto
status: seedling
source: "session 2026-08-18 leo-customer360 deployments/postgres"
tags: [terraform, vngcloud, vdb, postgres, psql, docker, gotcha]
---

# Running post-deploy SQL against a managed vDB (psql \gexec, dockerized client, private-IP caveat)

To run post-deploy `.sql` scripts against a GreenNode/VNG managed vDB (PostgreSQL) from Terraform, wire a `run-sql.sh` into `deploy.sh`s apply path (after a successful `terraform apply`).

Key details learned:
- **Get the endpoint from outputs**: the `vngcloud_vdb_relational_database` resource exports `ip` (List, computed) and `port` (Int, computed) — expose `db_host = try(element(ip,0),"")` + `db_port` as outputs, then `terraform output -raw db_host/db_port` in the script.
- **`\gexec` needs real psql**: a conditional `CREATE DATABASE` written as `SELECT ... WHERE NOT EXISTS (...) \gexec` is a psql meta-command, so a generic SQL driver wont run it. If `psql` isnt on PATH, fall back to a dockerized client: `docker run --rm -i -e PGPASSWORD=... postgres:16-alpine psql -v ON_ERROR_STOP=1 -h HOST -p PORT -U USER -d DB -f -` and pipe the file on **stdin** (avoids Windows/Rancher volume-mount path issues).
- **CREATE DATABASE cant be in a transaction**: run psql in default autocommit (do NOT use `--single-transaction`).
- **Connectivity is the real blocker**: with `public_access = false` the vDB IP is private (10.x) and only reachable from inside the VPC/VPN — a laptop/docker run cant connect. Options: run from a bastion in the VPC, or temporarily `public_access = true` + `allowed_ip_prefix` = your public IP.
- Order scripts by filename (`find -iname "*.sql" | sort`); guard idempotency with `CREATE EXTENSION IF NOT EXISTS` / `WHERE NOT EXISTS`.

## Related
[[GreenNode vDB package family + IOPS tier are per-zone (HCM03-1A vs 1C)]]

## Related

- [[GreenNode vDB package family + IOPS tier are per-zone (HCM03-1A vs 1C)]]
