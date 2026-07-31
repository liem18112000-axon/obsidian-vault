---
title: "Rotate credentials by verifying the new one before deleting the old"
created: 2026-06-14
type: lesson
status: seedling
source: "session 2026-06-14, accesstrade_integration migrate-to-wif"
tags: [security, credentials, rotation, ci-cd, gcp]
---

# Rotate credentials by verifying the new one before deleting the old

**When rotating any credential (SA key, token, password), establish and VERIFY the replacement is working BEFORE deleting the old one — never the reverse. A failure mid-rotation then leaves the system on the still-valid old credential instead of locked out with none.**

Concrete shape (migrate-to-wif.sh: long-lived GCP SA key → keyless WIF):
1. Set up the new credential path (create WIF pool/provider/binding, set the GCP_WIF_PROVIDER secret).
2. **Verify** it's actually in place — `gh secret list | grep -qx GCP_WIF_PROVIDER` — and ABORT if not.
3. Only then delete the old: the SA's user-managed keys, then the GCP_CREDENTIALS_JSON secret.

The order is the whole safety property. The verify gate (step 2) is what makes it safe to automate / re-run: if WIF setup silently failed, the script stops before destroying the working key. Make the migration idempotent (guarded creates, 'already gone' branches) so a re-run after partial success completes cleanly.

General rule: add-then-verify-then-remove. Applies to key rotation, DNS cutovers, swapping a primary, etc. Relates to [[Set up GitHub Actions to GCP via Workload Identity Federation]] and [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]].
