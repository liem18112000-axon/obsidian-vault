---
title: "Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider"
created: 2026-06-14
type: lesson
status: seedling
source: "accesstrade deployment/ Terraform, 2026-06-14"
tags: [gcp, cloud-sql, terraform, security, gotcha]
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
