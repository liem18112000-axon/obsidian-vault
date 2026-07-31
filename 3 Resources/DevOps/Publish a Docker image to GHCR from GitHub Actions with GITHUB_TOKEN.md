---
title: "Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN"
created: 2026-06-14
type: howto
status: seedling
source: "session 2026-06-14, accesstrade_integration"
tags: [github-actions, ghcr, docker, ci-cd]
---

# Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN

**You can push images to the GitHub Container Registry (ghcr.io) from Actions using the built-in `GITHUB_TOKEN` — no PAT needed — as long as the job grants `permissions: packages: write`.** Log in with `docker/login-action` (registry `ghcr.io`, username `${{ github.actor }}`, password `${{ secrets.GITHUB_TOKEN }}`), then `docker/metadata-action` + `docker/build-push-action`.

Recipe that works well:
```yaml
permissions: { contents: read, packages: write }
env: { IMAGE_NAME: ${{ github.repository }} }   # ghcr name = owner/repo, must be lowercase
# metadata-action tags: branch, pr, semver {{version}} & {{major}}.{{minor}}, sha, and
#   type=raw,value=latest,enable={{is_default_branch}}
# build-push-action: push: ${{ github.event_name != 'pull_request' }}, cache-from/to type=gha
```

Key points / gotchas:
- **PR builds should build but NOT push** (`push: ${{ github.event_name != 'pull_request' }}`) and skip the login step (`if: github.event_name != 'pull_request'`) — a fork PR has no write token, and you still want the Dockerfile validated.
- The ghcr image name must be **lowercase**; `metadata-action` lowercases `github.repository` for you.
- First push creates a package linked to the repo; `packages: write` + `GITHUB_TOKEN` is enough (no manual PAT/secret).
- `cache-from/to: type=gha` gives free layer caching across runs.
- Gate the build behind a `test` job (`needs: test`) so broken images never publish.
- Validating the workflow locally with PyYAML throws `KeyError: 'on'` because YAML 1.1 parses the `on:` key as boolean `True` — harmless, GitHub Actions reads it correctly.
