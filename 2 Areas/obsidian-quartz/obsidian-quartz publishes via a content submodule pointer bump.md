---
title: "obsidian-quartz publishes via a content submodule pointer bump"
created: 2026-07-31
type: concept
status: seedling
source: "session 2026-07-31 quartz vault sync"
tags: [quartz, obsidian, git-submodule, github-pages, publishing]
---

# obsidian-quartz publishes via a content submodule pointer bump

The published site at `obsidian-quartz` (Quartz static-site generator) does **not** contain the notes directly. Its `content/` directory is a **git submodule** pointing at the separate `obsidian-vault` repo. Two repos, two push targets.

**Publish flow (`publish.sh`):**
1. In `content/` (the vault): commit any edits, reconcile with the vault remote, and push → `obsidian-vault` advances.
2. In the Quartz superproject: `git add content` to bump the recorded submodule pointer to the new vault HEAD, commit `(vault bump)`, and push → `obsidian-quartz` advances.
3. The push to `obsidian-quartz` triggers the GitHub Actions **"Deploy Quartz site to GitHub Pages"** workflow (~3 min) which builds and publishes.

**Implications:**
- A vault edit is not live until *both* pushes happen AND the pointer is bumped — pushing only the vault does nothing to the site.
- Watch deploys at `https://github.com/liem18112000-axon/obsidian-quartz/actions`.
- Do the bump with `git add content`, not `git submodule update --remote` — see [[git submodule update --remote can clobber unpushed submodule HEAD]].
- Reconcile the vault safely with [[Classify local vs upstream with git merge-base to pick ff or rebase]].

## Related

- [[git submodule update --remote can clobber unpushed submodule HEAD]]
- [[Classify local vs upstream with git merge-base to pick ff or rebase]]
