---
title: "GCP Cloud SQL IAM role cheat-sheet: which role grants cloudsql.instances.get"
created: 2026-07-26
type: howto
status: seedling
source: "session 2026-07-26"
tags: [gcp, iam, cloudsql, gcloud]
---

# GCP Cloud SQL IAM role cheat-sheet: which role grants cloudsql.instances.get

When a GCP action fails with **"Permission `cloudsql.instances.get` denied"**, the fix is a Cloud SQL predefined role. Pick by *intent*, not by the single permission name:

| Intent | Role | Key permissions |
| --- | --- | --- |
| **Connect to** an instance (Auth Proxy / connectors) — the usual fix | `roles/cloudsql.client` | `cloudsql.instances.get` + `cloudsql.instances.connect` |
| View instance metadata only (read-only) | `roles/cloudsql.viewer` | `cloudsql.instances.get` + `list` |
| IAM database authentication (login as IAM DB user) | `roles/cloudsql.instanceUser` | `connect` + `get` + `login` |
| Full administration | `roles/cloudsql.admin` | everything |

`cloudsql.instances.get` alone appears in all of the above, so "get denied" does not tell you which role is needed — decide from whether the member must **connect** (client), just **view** (viewer), or **log in as a DB user** (instanceUser).

Grant with:
```bash
gcloud projects add-iam-policy-binding PROJECT \
  --member="user:EMAIL" --role="roles/cloudsql.client" --condition=None
```
Revoke by mirroring with `remove-iam-policy-binding`. Setting IAM needs `resourcemanager.projects.setIamPolicy` (Project IAM Admin / Owner) on the acting account.

## Related

- [[Skill google-skill-grant-cloudsql-access grants Cloud SQL role by email (preview-first)]]
