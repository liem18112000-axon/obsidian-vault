---
title: "GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern"
created: 2026-06-04
type: howto
status: seedling
source: "session 2026-06-04, LEO CDP CI pipeline"
tags: [github-actions, docker, ghcr, dockerhub, ci-cd]
---

# GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern

A GitHub Actions publishing workflow can target **GHCR by default and add Docker Hub only when credentials are configured**, so the same workflow runs cleanly in forks/clones that lack Docker Hub secrets.

**Pattern:**
- A `detect` job reads `secrets.DOCKER_USERNAME`/`secrets.DOCKER_PASSWORD` into a step's `env:` (secrets can't be referenced directly in `if:`), then sets a job output: `has_dockerhub=true|false`.
- The Docker Hub **login** step and the **tag-assembly** step gate on it: `if: needs.detect.outputs.has_dockerhub == 'true'`.
- GHCR login always runs — `GITHUB_TOKEN` is automatic and needs only `permissions: packages: write`.

**Why:** zero extra setup for the common case (GHCR), graceful opt-in for Docker Hub, and no hard failure when secrets are absent. Observed in the open-notebook / leo-agentic-notebook workflows and reused in the LEO CDP `release.yml`.

Note: secrets are empty strings (not errors) when unset, so `[[ -n "$DH_USER" && -n "$DH_PASS" ]]` is the detection test.

## Related

- [[Trivy scan-before-push needs a single-arch load build first]]
