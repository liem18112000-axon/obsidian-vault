---
title: "Pipe a GCP service-account key straight into a GitHub secret without leaking it"
created: 2026-06-14
type: howto
status: seedling
source: "session 2026-06-14, accesstrade_integration CI auth"
tags: [gcp, secrets, github-actions, security, service-account, ci-cd]
---

# Pipe a GCP service-account key straight into a GitHub secret without leaking it

**When you must use a service-account key JSON (e.g. WIF isn't available), create it to a temp file OUTSIDE the repo, pipe it straight into the secret store, and delete it — the key material should never hit repo-tracked disk, terminal output, or a knowledge note.**

Safe one-liner pattern (GCP key -> GitHub repo secret):
```bash
KEYFILE="$(mktemp)"; trap 'rm -f "$KEYFILE"' EXIT
gcloud iam service-accounts keys create "$KEYFILE" --iam-account=SA_EMAIL --project PROJECT
gh secret set GCP_CREDENTIALS_JSON --repo OWNER/REPO < "$KEYFILE"
```
Why each part: `mktemp` puts it in the OS temp dir (not the repo, so it can't be git-added); `gcloud keys create` writes the JSON to the file and only prints the key *id* to stderr (never the material); `gh secret set ... < file` reads from the file with no echo; the `trap ... EXIT` deletes the key even if a step fails. Verify afterward with `gh secret list` (shows names only) and confirm the temp file is gone.

Hard rules: never `cat` the key, never pass it as a CLI arg that lands in shell history/process list, never write it under the repo tree, never store it in notes/vault.

Operational gotcha learned the same day: an account can have project IAM-policy perms (create SAs, bind roles) yet STILL lack `iam.workloadIdentityPools.create` (that needs roles/iam.workloadIdentityPoolAdmin). When WIF setup is blocked by that, the SA-key fallback unblocks CI — at the cost of a long-lived key, so plan to rotate to WIF later. Org policy `iam.disableServiceAccountKeyCreation` can block the fallback too. Relates to [[Set up GitHub Actions to GCP via Workload Identity Federation]] and [[Secrets handling for affiliate API keys]].
