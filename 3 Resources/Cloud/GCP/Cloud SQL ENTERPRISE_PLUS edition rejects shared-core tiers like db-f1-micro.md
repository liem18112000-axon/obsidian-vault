---
ai_hash: 95d405ba703004cd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: session 2026-06-14
status: seedling
tags:
- gcp
- cloud-sql
- terraform
- gotcha
title: Cloud SQL ENTERPRISE_PLUS edition rejects shared-core tiers like db-f1-micro
type: gotcha
---

# Cloud SQL ENTERPRISE_PLUS edition rejects shared-core tiers like db-f1-micro

Google Cloud SQL has two editions — **ENTERPRISE** and **ENTERPRISE_PLUS** — and the instance edition constrains which machine tiers are legal. ENTERPRISE_PLUS only accepts `db-perf-optimized-N-*` tiers; it **rejects the cheap shared-core tiers** `db-f1-micro` and `db-g1-small`.

In some projects/regions a new `google_sql_database_instance` defaults to ENTERPRISE_PLUS, so a Terraform apply with `tier = "db-f1-micro"` fails at create time with:

```
Error 400: Invalid request: Invalid Tier (db-f1-micro) for (ENTERPRISE_PLUS) Edition.
Use a predefined Tier like db-perf-optimized-N-* instead.
```

**Fix:** pin the edition explicitly in the `settings` block so the cheap tier stays valid:

```hcl
settings {
  tier    = "db-f1-micro"
  edition = "ENTERPRISE"
}
```

Use ENTERPRISE for nonprod/cost-sensitive instances (shared-core tiers, ~no HA floor); reach for ENTERPRISE_PLUS only when you actually want its perf-optimized tiers and features. Surfaced fixing the accesstrade_integration `deployment/` Terraform (database.tf).

%% ai-graph-start %%

**Related notes:**
- [[Cloud SQL Postgres point-in-time recovery requires automated backups enabled]]
- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]

%% ai-graph-end %%