---
title: "Set up GitHub Actions to GCP via Workload Identity Federation"
created: 2026-06-14
type: howto
status: seedling
source: "session 2026-06-14, accesstrade_integration deployment"
tags: [gcp, workload-identity, github-actions, oidc, iam, ci-cd, security]
---

# Set up GitHub Actions to GCP via Workload Identity Federation

**To let GitHub Actions authenticate to GCP without a long-lived key, use Workload Identity Federation: a WIF pool + a GitHub OIDC provider, a deploy service account the repo is allowed to impersonate, and two repo secrets (`GCP_WIF_PROVIDER`, `GCP_SERVICE_ACCOUNT`) the `google-github-actions/auth@v2` step consumes. The workflow job needs `permissions: id-token: write`.**

One-time gcloud setup (PROJECT_NUMBER = `gcloud projects describe PROJECT --format='value(projectNumber)'`):
1. `gcloud services enable iamcredentials.googleapis.com sts.googleapis.com iam.googleapis.com`
2. create the deploy SA; grant it ONLY the roles the IaC needs (e.g. for Cloud SQL + Memorystore + VPC: `serviceusage.serviceUsageAdmin`, `cloudsql.admin`, `redis.admin`, `compute.networkAdmin`).
3. `workload-identity-pools create POOL --location=global`
4. `workload-identity-pools providers create-oidc PROVIDER --issuer-uri=https://token.actions.githubusercontent.com --attribute-mapping='google.subject=assertion.sub,attribute.repository=assertion.repository' --attribute-condition="assertion.repository=='OWNER/REPO'"`
5. bind impersonation: `gcloud iam service-accounts add-iam-policy-binding SA --role=roles/iam.workloadIdentityUser --member='principalSet://iam.googleapis.com/projects/NUMBER/locations/global/workloadIdentityPools/POOL/attribute.repository/OWNER/REPO'`
6. the two secret values: provider = `projects/NUMBER/locations/global/workloadIdentityPools/POOL/providers/PROVIDER`; service account = the SA email. Set with `gh secret set`.

**Security-critical gotcha:** the `--attribute-condition` pinning `assertion.repository=='OWNER/REPO'` (and binding the `principalSet` to `attribute.repository/OWNER/REPO`, not the whole pool) is mandatory. Without it ANY GitHub repo's workflow can mint tokens for your pool and impersonate the SA. gcloud now refuses to create a GitHub OIDC provider without an attribute-condition for this reason.

Idempotency: guard each create with a `describe || create`; `add-iam-policy-binding` is naturally idempotent. Use `--condition=None` on role bindings to skip the interactive IAM-condition prompt in scripts.

Key-JSON fallback (discouraged, long-lived): skip WIF, create a SA key, `gh secret set GCP_CREDENTIALS_JSON < key.json`, and the auth step uses `credentials_json` instead. Prefer WIF.

Relates to [[GCP auth ambient ADC in GCP-hosted runners vs explicit creds in external CI]] (WIF is the 'external CI' answer) and [[Gate a GitHub Actions job on secret presence via a preflight job output]].
