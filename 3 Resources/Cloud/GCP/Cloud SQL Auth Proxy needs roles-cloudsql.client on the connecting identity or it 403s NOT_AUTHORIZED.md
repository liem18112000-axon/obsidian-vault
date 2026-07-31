---
title: "Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED"
created: 2026-07-06
type: gotcha
status: seedling
source: "vinnstack connect-cloud-db.ps1, 2026-07-06"
tags: [cloud-sql, iam, gcp, auth-proxy, vinnstack]
---

# Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED

To connect through the Cloud SQL Auth Proxy, the identity the proxy authenticates as (your gcloud Application Default Credentials, or the GSA on a pod) needs the role roles/cloudsql.client on the instance's PROJECT. It grants cloudsql.instances.connect + cloudsql.instances.get. Missing it → the proxy starts but the connection fails with 403 NOT_AUTHORIZED (surfaces app-side as ECONNRESET / "cannot connect"). The DB user/password is separate and unrelated — this is purely IAM to reach the instance, not to log into Postgres.

Check a member's roles on a project:
  gcloud projects get-iam-policy PROJECT --flatten="bindings[].members" --filter="bindings.members:EMAIL" --format="value(bindings.role)"
Grant it (single command; pick user: or serviceAccount: by whether the email ends in .gserviceaccount.com):
  gcloud projects add-iam-policy-binding PROJECT --member="user:EMAIL" --role="roles/cloudsql.client"

Gotchas: (1) the check needs resourcemanager.projects.getIamPolicy yourself, else it errors — treat a failed check as inconclusive, not "missing"; (2) the role can be inherited via a group, so a direct-binding check can false-negative — keep such checks a WARNING, never a hard block; (3) IAM policy binding changes on GCP typically require a human with the right admin role — an automated agent usually can't self-grant.
