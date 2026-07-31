---
title: "CI: build Docker image on every run, push only on non-PR"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml 2026-06-06"
tags: [github-actions, docker, ci, pull-request, pattern]
---

# CI: build Docker image on every run, push only on non-PR

A Docker build step gated `if: github.event_name != pull_request` does NOT run on PRs — so the image is never built on a PR and a broken Dockerfile (build or runtime stage) passes CI silently. The publish is correctly PR-gated, but conflating *build* and *push* into one gated step loses PR validation.

**Pattern: always build, conditionally push.** Let the build run on every event and make only the *push* conditional:

```yaml
- name: Log in to GHCR
  if: github.event_name != pull_request      # cant / shouldnt publish from a PR
  uses: docker/login-action@v3
  with: { registry: ghcr.io, username: ${{ github.actor }}, password: ${{ secrets.GITHUB_TOKEN }} }
- name: Build image (push only on non-PR)
  uses: docker/build-push-action@v6
  with:
    push: ${{ github.event_name != pull_request }}   # build always, push only off-PR
    tags: ...
```

`docker/build-push-action` builds regardless; with `push: false` it just builds (no registry auth needed), so login can stay PR-gated. Net effect: PRs verify the Dockerfile end-to-end without publishing; main/dev builds also push. Same idea applies to any "build vs publish" split (npm pack vs publish, etc.).

## Related
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[GitHub Actions continue-on-error: step-level goes green, job-level stays red]]

## Related

- [[LEO CDP CI provisions deps CI-natively]]
- [[pinned to devops-script versions for parity]]
- [[GitHub Actions continue-on-error: step-level goes green]]
- [[job-level stays red]]
