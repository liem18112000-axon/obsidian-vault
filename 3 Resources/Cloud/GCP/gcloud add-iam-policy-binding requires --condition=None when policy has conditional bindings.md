---
ai_hash: aa1bb0bdaf4865c2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: session 2026-07-07
status: seedling
tags:
- gcp
- gcloud
- iam
- gotcha
title: gcloud add-iam-policy-binding requires --condition=None when policy has conditional
  bindings
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]
- [[artifactregistry.repoAdmin cannot grant IAM despite the name]]
- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]
- [[IAM roles a CI service account needs to build and deploy to Cloud Run]]

%% ai-graph-end %%