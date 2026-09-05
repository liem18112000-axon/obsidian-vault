---
title: "A git-ignored secret file that a deploy script silently degrades on is a CI foot-gun"
created: 2026-08-22
type: lesson
status: seedling
source: "session 2026-08-22"
tags: [ci-cd, secrets, github-actions, deployment, gotcha]
---

# A git-ignored secret file that a deploy script silently degrades on is a CI foot-gun

When a deploy script reads a **git-ignored secret file** (e.g. a `.env` produced by a local bootstrap step) and *silently falls back* to a degraded mode if the file is missing, CI becomes a foot-gun: the file exists on the developer laptop but never on the runner, so the pipeline quietly ships the degraded config on every run while it "works on my machine."

## Two rules that defuse it
1. **CI must reconstruct the secret file from a secrets store** (GitHub Actions secret, Vault, etc.) before invoking the deploy script — do not rely on a git-ignored artifact being present.
2. **Make the degrade branch loud.** The fallback path should emit a visible warning (GitHub Actions: `::warning::`) so a silent false-fallback shows up in the logs instead of being discovered in production behavior.

## Why it hides so well
The happy path passes locally (file present), the pipeline exits 0 (fallback is "graceful"), and the wrong outcome is a *behavioral* difference (a feature flag flips off), not a build failure — so nothing red ever appears.

Concrete instance: [[leo-customer360 frontend SSO=false because CD deploys the API with SSO_LOGIN=false]].

## Related

- [[leo-customer360 frontend SSO=false because CD deploys the API with SSO_LOGIN=false]]
