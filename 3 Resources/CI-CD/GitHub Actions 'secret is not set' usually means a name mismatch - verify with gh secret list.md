---
title: "GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04 fb-info-project run 28699630650"
tags: [github-actions, secrets, debugging, gotcha]
---

# GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list

When a GitHub Actions job reports a secret as empty/"not set" even though the operator swears they configured it, the usual cause is a NAME or SCOPE mismatch, not a missing value:

- Wrong name: `${{ secrets.LICENSE_PRIVKEY }}` reads empty if the secret was saved as `LICENSE_PUBKEY` (or any other spelling). Referencing an undefined secret is NOT an error - it silently expands to empty string, so the failure surfaces only in your own guard.
- Wrong scope: a secret set as an *Environment* secret, or under *Dependabot*/*Codespaces*, is invisible to a normal Actions `secrets.*` reference unless the job opts into that environment.
- Wrong repo/fork.

Diagnose with `gh secret list --repo <owner>/<repo>` (repo Actions secrets) and `gh api repos/<owner>/<repo>/environments` (env-scoped). The fix is almost always re-adding under the exact name the workflow references. Guard against silent-empty by failing fast in the job (`if not value: sys.exit("::error::...")`).

Related: [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
