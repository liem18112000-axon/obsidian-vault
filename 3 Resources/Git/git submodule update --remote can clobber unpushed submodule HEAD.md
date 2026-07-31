---
title: "git submodule update --remote can clobber unpushed submodule HEAD"
created: 2026-07-31
type: lesson
status: seedling
source: "session 2026-07-31 quartz vault sync"
tags: [git, submodule, gotcha]
---

# git submodule update --remote can clobber unpushed submodule HEAD

`git submodule update --remote <sub>` fetches the submodule's configured remote-tracking branch and checks the submodule out to **that branch tip** (e.g. `origin/main`) — not to whatever commit the submodule working tree currently holds.

So in a publish script, running it *after* you have locally advanced the submodule (a fresh commit, a rebase, or commits not yet pushed) can silently **move the pointer backward** to the old remote HEAD, discarding your new work before the superproject even records it.

**Fix / rule:** once the submodule working tree is already at the commit you want to publish, just `git add <sub>` in the superproject to record the checked-out SHA. Only use `--remote` when you explicitly want to *pull* the submodule up to its remote branch and you know local has nothing unpushed.

This bit the `obsidian-quartz` publish flow — see [[obsidian-quartz publishes via a content submodule pointer bump]] and the reconciliation technique in [[Classify local vs upstream with git merge-base to pick ff or rebase]].

## Related

- [[obsidian-quartz publishes via a content submodule pointer bump]]
- [[Classify local vs upstream with git merge-base to pick ff or rebase]]
