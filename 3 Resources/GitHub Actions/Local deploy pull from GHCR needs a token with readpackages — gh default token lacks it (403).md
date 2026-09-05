---
title: "Local deploy pull from GHCR needs a token with read:packages — gh default token lacks it (403)"
created: 2026-08-21
type: lesson
status: seedling
source: "session 2026-08-21, deploy-all.sh uat"
tags: [ghcr, read-packages, gh-cli, docker, gotcha, 403]
---

# Local deploy pull from GHCR needs a token with read:packages — gh default token lacks it (403)

Pulling a **private** GHCR image on a deploy target needs a token with the `read:packages` scope. The GitHub CLI default token (`gh auth token`) typically has `gist, read:org, repo, workflow` and **not** `read:packages`, so `docker pull ghcr.io/<org>/<repo>/<svc>` returns **403 Forbidden** ("unexpected status from HEAD request … 403"). GitHub **Actions** `GITHUB_TOKEN` with `permissions: packages: read` works — which is why CD pulls fine but a local run with `GHCR_TOKEN=$(gh auth token)` fails.

Fixes for local deploys: (1) `gh auth refresh -h github.com -s read:packages` then reuse `gh auth token`; (2) create a classic PAT with `read:packages` and export it as the pull token; (3) make the package public (then no auth needed). Note: `docker login` still "succeeds" with an under-scoped token — the 403 only surfaces on the pull. Verified in leo-customer360: local `./deploy-all.sh uat --only …` resolved everything from remote state and SSHed to the box, failing only at the GHCR pull for this reason. Related: [[Get the newest GHCR image tag or digest via gh api packages container versions]].

## Related

- [[Get the newest GHCR image tag or digest via gh api packages container versions]]
