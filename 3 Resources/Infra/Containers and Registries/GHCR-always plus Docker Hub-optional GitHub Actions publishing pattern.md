---
ai_hash: b835325b907b40fb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities: []
source: session 2026-06-04, LEO CDP CI pipeline
status: seedling
tags:
- github-actions
- docker
- ghcr
- dockerhub
- ci-cd
title: GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Trivy scan-before-push needs a single-arch load build first]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[GCP auth ambient ADC in GCP-hosted runners vs explicit creds in external CI]]

%% ai-graph-end %%