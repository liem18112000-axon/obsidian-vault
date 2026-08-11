---
ai_hash: ae3240a3d6d12cef
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities: []
source: appsflyer-data-connector infra.yml, 2026-06-24
status: seedling
tags:
- github-actions
- terraform
- ci-cd
- secrets
- gotcha
- deployment
title: Gate Terraform/deploy CI to push-on-main, not pull_request (secrets fail PRs)
type: gotcha
---

# Gate Terraform/deploy CI to push-on-main, not pull_request (secrets fail PRs)

A GitHub Actions workflow that runs Terraform (or any deploy needing cloud secrets) **must not** use a `pull_request` trigger if those secrets are required to succeed: PRs from feature branches/forks either lack the secrets (forks get none by default) or shouldnt apply infra, so the job **fails on every PR** with auth/`init` errors and shows a red check.

**Fix:** gate apply-style workflows to `push: branches: [main]` only (optionally with `paths:` filters), not `pull_request`:

```yaml
on:
  push:
    branches: [main]
    paths: ["terraform/**"]
```

Review the infra change on the PR *diff* instead; the apply happens when it merges to main. If you do want a PR-time `terraform plan` for visibility, run it in a separate read-only job whose creds are scoped to plan, and remember fork PRs still wont have secrets. Removing the `pull_request` trigger also lets you drop the now-redundant `pull-requests: write` permission and the `if: github.ref == main` guard on the apply step.

See [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]].

## Related

- [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]]

%% ai-graph-start %%

**Related notes:**
- [[Gate Terraform apply to create-if-absent except on the release branch]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Cloud Build repo connection blocked drive build+deploy from GitHub Actions instead]]
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]

%% ai-graph-end %%