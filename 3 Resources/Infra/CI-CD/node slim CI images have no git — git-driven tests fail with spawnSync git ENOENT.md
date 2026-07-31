---
ai_hash: 6d1e20f047e008cb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-14
status: seedling
tags:
- ci
- cloud-build
- git
- docker
- gotcha
- vitest
title: node slim CI images have no git — git-driven tests fail with spawnSync git
  ENOENT
type: lesson
---

# node slim CI images have no git — git-driven tests fail with spawnSync git ENOENT

Gotcha (Vinnstack Cloud Build, 2026-07-14): the `test` step ran on `node:20-bookworm-slim`, which does NOT ship git. The BDD tests (test/lib/bdd/implementGit.test.ts) drive real git (git init / worktree / commit / push against a local bare repo), so all 18 failed with `spawnSync git ENOENT` — failing the build's test gate before the Electron exe was ever packaged. They pass locally because dev machines have git.

Fix: in the CI test step, `apt-get update && apt-get install -y --no-install-recommends git && git config --global user.email … && git config --global user.name … && npm run test`. The identity config matters too: the commit/worktree tests need a committer identity, else git fails with "Author identity unknown" once git itself is present.

Lesson: `-slim` / alpine base images strip git (and other CLIs). If any test or build step shells out to git, either install it in that step or pick a fuller image. tsc/typecheck won't catch this — only running the suite in the real CI image does. Confirm suspected env-only failures by running the exact failing test file locally (where the tool IS present) before "fixing" the code.

%% ai-graph-start %%

**Related notes:**
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[Re-check live dependencies right before committing in a shared repo]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]

%% ai-graph-end %%