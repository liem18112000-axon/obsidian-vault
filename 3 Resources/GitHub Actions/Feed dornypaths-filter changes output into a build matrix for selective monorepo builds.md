---
title: "Feed dorny/paths-filter changes output into a build matrix for selective monorepo builds"
created: 2026-08-20
type: howto
status: seedling
source: "session 2026-08-20 leo-customer360 CI"
tags: [github-actions, monorepo, ci, docker]
---

# Feed dorny/paths-filter changes output into a build matrix for selective monorepo builds

The `dorny/paths-filter` action exposes an output called `changes` that is a **JSON array of the filter names whose paths were touched** by the diff (e.g. `["ads-server","frontend-admin"]`). Promote it to a job output and feed it straight into a matrix to build only what changed in a monorepo.

```yaml
changes:
  outputs:
    services: ${{ steps.filter.outputs.changes }}
  steps:
    - uses: dorny/paths-filter@v3
      id: filter
      with:
        filters: |
          ads-server: 'ads-server/**'
          frontend-admin: 'frontend-admin/**'

build:
  needs: changes
  if: ${{ needs.changes.outputs.services != '[]' }}
  strategy:
    matrix:
      service: ${{ fromJSON(needs.changes.outputs.services) }}
```

Guard the build job with `if: != '[]'` so it is skipped cleanly when nothing relevant changed. Adding a service is then a one-line filter change. Established while wiring GHCR builds for leo-customer360.

Related: [[GHCR image names must be lowercase; docker-metadata-action lowercases automatically]], [[Workflow-level paths-ignore can stop a workflow triggering on the dir you want to build]].

## Related

- [[GHCR image names must be lowercase; docker/metadata-action lowercases automatically]]
- [[Workflow-level paths-ignore can stop a workflow triggering on the dir you want to build]]
