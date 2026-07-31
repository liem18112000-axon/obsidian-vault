---
ai_hash: ee68219170fbf983
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project run 28699630650
status: seedling
tags:
- github-actions
- secrets
- debugging
- gotcha
title: GitHub Actions 'secret is not set' usually means a name mismatch - verify with
  gh secret list
type: lesson
---

# GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list

When a GitHub Actions job reports a secret as empty/"not set" even though the operator swears they configured it, the usual cause is a NAME or SCOPE mismatch, not a missing value:

- Wrong name: `${{ secrets.LICENSE_PRIVKEY }}` reads empty if the secret was saved as `LICENSE_PUBKEY` (or any other spelling). Referencing an undefined secret is NOT an error - it silently expands to empty string, so the failure surfaces only in your own guard.
- Wrong scope: a secret set as an *Environment* secret, or under *Dependabot*/*Codespaces*, is invisible to a normal Actions `secrets.*` reference unless the job opts into that environment.
- Wrong repo/fork.

Diagnose with `gh secret list --repo <owner>/<repo>` (repo Actions secrets) and `gh api repos/<owner>/<repo>/environments` (env-scoped). The fix is almost always re-adding under the exact name the workflow references. Guard against silent-empty by failing fast in the job (`if not value: sys.exit("::error::...")`).

Related: [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]

%% ai-graph-start %%

**Related notes:**
- [[secrets context is not available in GitHub Actions if conditions]]
- [[GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging as means it equals a secret]]
- [[GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access]]
- [[GitHub Actions gives fallback defaults because empty string is falsy in expressions]]
- [[GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern]]

%% ai-graph-end %%