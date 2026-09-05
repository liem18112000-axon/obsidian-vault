---
title: "Inject a Postgres session GUC into an unmodifiable script via PGOPTIONS"
created: 2026-08-19
type: lesson
status: seedling
source: "leo-customer360 seed_data.sh 2026-08-19"
tags: [postgresql, rls, pgoptions, libpq, seeding, gotcha]
---

# Inject a Postgres session GUC into an unmodifiable script via PGOPTIONS

To run a seed/ETL script that writes RLS-protected tables but does NOT itself set the
tenant GUC (`app.tenant_id`), set it for every libpq connection from OUTSIDE the code via
the `PGOPTIONS` environment variable — no script edit needed:

    docker run -e PGOPTIONS="-c app.tenant_id=<tenant-uuid>" ...

libpq applies PGOPTIONS as connection startup options when the code calls
`psycopg2.connect(host=..., dbname=..., ...)` without an explicit `options=` arg, so the
GUC is set at session start for every connection the script opens. The RLS policy
`current_setting(app.tenant_id, true)::uuid` then evaluates to the tenant and the
FORCE-RLS DELETE/INSERT for that tenant succeed under a plain (non-superuser) role.

Only valid when all rows belong to ONE tenant (a single PGOPTIONS value). Transaction-local
`set_config(...,true)` calls inside the script still override it per-transaction, which is
fine. Works for any libpq client (psql honors PGOPTIONS too).

Used in leo-customer360 deployments/server/seed_data.sh to run the CIR demo seed
(init_sample_data / run_demo_resolution / seed_full_demo_data — none set app.tenant_id)
against the managed non-superuser DB. Related: [[FORCE RLS breaks seeding as a non-superuser unless app.tenant_id is set]]

## Related

- [[FORCE RLS breaks seeding as a non-superuser unless app.tenant_id is set]]
