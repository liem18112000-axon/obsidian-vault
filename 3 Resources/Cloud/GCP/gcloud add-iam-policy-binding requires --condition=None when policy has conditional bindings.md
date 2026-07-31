---
title: "gcloud add-iam-policy-binding requires --condition=None when policy has conditional bindings"
created: 2026-07-07
type: lesson
status: seedling
source: "session 2026-07-07"
tags: [gcp, gcloud, iam, gotcha]
---

# gcloud add-iam-policy-binding requires --condition=None when policy has conditional bindings

Running `gcloud projects add-iam-policy-binding` in non-interactive mode fails with:

> Adding a binding without specifying a condition to a policy containing conditions is prohibited in non-interactive mode. Run the command again with `--condition=None`

This happens whenever the target IAM policy already has *any* conditional bindings (e.g. cluster-restricted roles) and you try to add a new unconditional binding. Fix: pass `--condition=None` explicitly on the new binding.

```
gcloud projects add-iam-policy-binding <project> \
  --member="user:someone@example.com" \
  --role="roles/some.role" \
  --condition=None
```

Applies to any `add-iam-policy-binding` call (projects, folders, orgs) once the resource policy has mixed conditional/unconditional bindings — not specific to Artifact Registry.

## Related

- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]
