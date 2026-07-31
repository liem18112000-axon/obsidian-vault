---
ai_hash: c5551fd9acf6547c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-06
entities: []
source: vinnstack main-public deploy 2026-07-06
status: seedling
tags:
- cloud-build
- gcp
- trigger
- ci-cd
- vinnstack
title: Change a 2nd-gen Cloud Build trigger's branch filter via beta export-edit-import,
  not update
type: gotcha
---

# Change a 2nd-gen Cloud Build trigger's branch filter via beta export-edit-import, not update

For a 2nd-gen Cloud Build trigger (one with a repositoryEventConfig — e.g. connected to Bitbucket/GitHub via a repository connection), you can't repoint the branch filter with a bare `gcloud builds triggers update <name> --branch-pattern=...`: `update` expects a SOURCE-TYPE SUBCOMMAND (github/bitbucket-server/manual/...) and errors "Invalid choice: <name>" for a repository-connection trigger. Also `export`/`import` live in the `beta` track, not GA.

Reliable, type-agnostic recipe:
  gcloud beta builds triggers export <name> --region=<r> --project=<p> --destination=t.yaml
  # edit t.yaml: repositoryEventConfig.push.branch  e.g.  ^main$ -> ^main-public$
  gcloud beta builds triggers import --source=t.yaml --region=<r> --project=<p>
Verify: gcloud builds triggers describe <name> --format="value(repositoryEventConfig.push.branch)".

Two gotchas that bite after repointing: (1) region matters — 2nd-gen triggers are region-scoped, so list/describe/export/import all need --region, and a global `triggers list` shows nothing. (2) The trigger only fires on branches that exist ON THE REMOTE; repointing to a branch that's still local-only does nothing until that branch is pushed — and the first push of it will itself fire a build.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%