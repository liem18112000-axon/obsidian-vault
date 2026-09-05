---
title: "Cloud Run's managed /cloudsql socket does not reach sidecar containers"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03"
tags: [gcp, cloud-run, cloud-sql, sidecar, gotcha]
---

# Cloud Run's managed /cloudsql socket does not reach sidecar containers

In a Cloud Run multi-container (sidecar) service, the automatic Cloud SQL connection — the `/cloudsql/<INSTANCE_CONNECTION_NAME>` unix socket the platform mounts when you attach a Cloud SQL instance — is only provisioned inside the **ingress** container, not in sidecar containers. A sidecar that opens that socket (e.g. asyncpg with `host=/cloudsql/<conn>`) dies with `FileNotFoundError [Errno 2]` because the socket was never created in its filesystem.

The trap: the service-level `cloud_sql_instance` volume and the sidecar's `volumeMounts:[/cloudsql]` all look correct in Terraform and in the Cloud Run v2 API, so nothing appears misconfigured — yet the managed proxy simply doesn't create the socket for the sidecar. A clean fresh revision / `terraform -replace` does not fix it either.

Fix: if your DB-using process runs in the sidecar (not the ingress container), stop relying on the mounted socket and connect **socketlessly** — use the Cloud SQL Python Connector, which dials the instance over the container's normal egress with IAM + TLS (needs `roles/cloudsql.client` + the Cloud SQL Admin API enabled) and therefore works from any container regardless of which one is ingress. Alternatives: run the Cloud SQL Auth Proxy as an explicit TCP sidecar and connect to 127.0.0.1, or make the DB container the ingress one.

## Related

- [[gcloud builds submit --suppress-logs still exits non-zero on the log-streaming permission error]]
