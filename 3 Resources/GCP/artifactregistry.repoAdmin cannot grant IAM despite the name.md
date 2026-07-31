---
title: "artifactregistry.repoAdmin cannot grant IAM despite the name"
created: 2026-07-07
type: lesson
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [gcp, iam, artifact-registry, gotcha]
---

# artifactregistry.repoAdmin cannot grant IAM despite the name

`roles/artifactregistry.repoAdmin` sounds like it should let you administer a repo'\''s access, but it only grants permissions to manage artifact *contents* — uploading, downloading, deleting, listing packages/versions/tags. It does **not** include `artifactregistry.repositories.setIamPolicy` or `getIamPolicy`.

Confirmed by running `gcloud iam roles describe roles/artifactregistry.repoAdmin` and finding no IAM-policy permissions in its `includedPermissions` list, then hitting a live `PERMISSION_DENIED` when trying `gcloud artifacts repositories add-iam-policy-binding` with only that role. Granting access to others on a repo requires `roles/artifactregistry.admin` (or a broader project-level IAM-admin/owner role) instead.

The general lesson: a GCP predefined role named "Admin" for a resource type does not necessarily include `setIamPolicy`/`getIamPolicy` on that resource — always check `includedPermissions` rather than assuming from the role name.

## Related
[[Publish arbitrary binaries to Artifact Registry with a generic repo]]
[[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]
