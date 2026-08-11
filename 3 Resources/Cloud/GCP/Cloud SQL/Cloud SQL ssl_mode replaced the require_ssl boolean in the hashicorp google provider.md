---
ai_hash: f10552394740f953
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: accesstrade deployment/ Terraform, 2026-06-14
status: seedling
tags:
- gcp
- cloud-sql
- terraform
- security
- gotcha
title: Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google
  provider
type: lesson
---

# Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider

In the `hashicorp/google` provider, a Cloud SQL instance's connection security is set through `settings.ip_configuration.ssl_mode`, **not** the old boolean `require_ssl`. Values:

- `ENCRYPTED_ONLY` — reject any non-TLS connection (use this to require SSL).
- `ALLOW_UNENCRYPTED_AND_ENCRYPTED` — permissive fallback.
- (`TRUSTED_CLIENT_CERTIFICATE_REQUIRED` — also mandates a client cert.)

Separately, a public-IP Cloud SQL instance (`ipv4_enabled = true`) is open to **no one** by default: until you add a CIDR to `authorized_networks`, nothing can connect over the public IP. The preferred alternative to widening that allow-list is the **Cloud SQL Auth Proxy**, which authenticates via IAM and is keyed on the instance **connection name** (`project:region:instance`, exposed as the `connection_name` attribute) rather than an IP — so you never expose the laptop's egress IP at all.

Seen provisioning the accesstrade_integration `deployment/` Terraform.

## Related
[[Memorystore Redis is always VPC-internal — no public endpoint]]
[[Cloud SQL Postgres point-in-time recovery requires automated backups enabled]]

## Related

- [[Memorystore Redis is always VPC-internal — no public endpoint]]
- [[Cloud SQL Postgres point-in-time recovery requires automated backups enabled]]

%% ai-graph-start %%

**Related notes:**
- [[Memorystore Redis is always VPC-internal — no public endpoint]]
- [[Cloud SQL Postgres point-in-time recovery requires automated backups enabled]]
- [[Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED]]
- [[GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get]]
- [[Gate Terraform apply to create-if-absent except on the release branch]]

%% ai-graph-end %%