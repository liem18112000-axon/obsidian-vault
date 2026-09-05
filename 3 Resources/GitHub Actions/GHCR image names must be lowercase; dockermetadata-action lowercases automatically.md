---
title: "GHCR image names must be lowercase; docker/metadata-action lowercases automatically"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20 leo-customer360 CI"
tags: [github-actions, ghcr, docker, gotcha]
---

# GHCR image names must be lowercase; docker/metadata-action lowercases automatically

GHCR (`ghcr.io`) rejects image paths containing uppercase letters. The `${{ github.repository }}` context preserves the org/repo casing exactly as created — e.g. `LEO-CDP/leo-customer360` — so naively using it in an image name yields an uppercase path that fails on push.

`docker/metadata-action` **lowercases the image name for you**, so this is safe:

```yaml
- uses: docker/metadata-action@v5
  with:
    images: ghcr.io/${{ github.repository }}/my-service
```

But if you construct the tag by hand (plain `docker build -t`, or string-building the ref), you must lowercase it yourself (e.g. `${OWNER,,}` in bash, or `tr A-Z a-z`). The gotcha only bites when the org/user name has capitals. Learned wiring GHCR pushes for the LEO-CDP org.

Related: [[Feed dorny/paths-filter changes output into a build matrix for selective monorepo builds]].

## Related

- [[Feed dorny/paths-filter changes output into a build matrix for selective monorepo builds]]
