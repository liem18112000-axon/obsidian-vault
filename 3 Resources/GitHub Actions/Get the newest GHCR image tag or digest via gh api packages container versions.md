---
title: "Get the newest GHCR image tag or digest via gh api packages container versions"
created: 2026-08-20
type: howto
status: seedling
source: "session 2026-08-20, leo-customer360"
tags: [ghcr, gh-cli, containers, cd, howto]
---

# Get the newest GHCR image tag or digest via gh api packages container versions

To resolve the **newest** image a CI pipeline pushed to GitHub Container Registry (for a CD pull step), query the GitHub Packages "container versions" API — it returns versions **newest-first by creation time**, so index `[0]` is the latest push:

```bash
# Package name = "<repo>/<service>" URL-encoded (the "/" becomes %2F).
gh api -H "Accept: application/vnd.github+json" \
  "/orgs/LEO-CDP/packages/container/leo-customer360%2Fcustomer360-api/versions?per_page=1" \
  --jq ".[0]"           # .name = digest (sha256:…); .metadata.container.tags = [tags]
```

- `.[0].name` is the immutable **digest** (`sha256:…`) → pull `ghcr.io/…/<svc>@sha256:…` for a fully pinned, reproducible deploy.
- `.[0].metadata.container.tags` lists the human tags on that push (e.g. `sha-<git-sha>`, `latest`, `v1.2.3`).

Use `/orgs/<ORG>/packages/...` for org-owned packages, `/users/<USER>/packages/...` for user-owned. Private packages need a token with `read:packages`. Alternatives: `crane ls`/`crane digest`, `skopeo inspect`, or `docker buildx imagetools inspect` — but the Registry v2 `/tags/list` does **not** guarantee chronological order, which is why the Packages API is preferred for "get the latest". Pin prod to an immutable tag/digest; let dev/uat float on `latest` or the newest `sha-*`. Context: [[leo-customer360 CD builds images on the VM instead of pulling from GHCR (CI/CD gap)]].

## Related

- [[leo-customer360 CD builds images on the VM instead of pulling from GHCR (CI/CD gap)]]
