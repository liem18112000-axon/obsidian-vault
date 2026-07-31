---
title: "node slim CI images have no git — git-driven tests fail with spawnSync git ENOENT"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [ci, cloud-build, git, docker, gotcha, vitest]
---

# node slim CI images have no git — git-driven tests fail with spawnSync git ENOENT

Gotcha (Vinnstack Cloud Build, 2026-07-14): the `test` step ran on `node:20-bookworm-slim`, which does NOT ship git. The BDD tests (test/lib/bdd/implementGit.test.ts) drive real git (git init / worktree / commit / push against a local bare repo), so all 18 failed with `spawnSync git ENOENT` — failing the build's test gate before the Electron exe was ever packaged. They pass locally because dev machines have git.

Fix: in the CI test step, `apt-get update && apt-get install -y --no-install-recommends git && git config --global user.email … && git config --global user.name … && npm run test`. The identity config matters too: the commit/worktree tests need a committer identity, else git fails with "Author identity unknown" once git itself is present.

Lesson: `-slim` / alpine base images strip git (and other CLIs). If any test or build step shells out to git, either install it in that step or pick a fuller image. tsc/typecheck won't catch this — only running the suite in the real CI image does. Confirm suspected env-only failures by running the exact failing test file locally (where the tool IS present) before "fixing" the code.
