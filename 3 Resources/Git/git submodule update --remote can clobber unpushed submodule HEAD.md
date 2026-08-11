---
ai_hash: 414fd405f8220f41
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: session 2026-07-31 quartz vault sync
status: seedling
tags:
- git
- submodule
- gotcha
title: git submodule update --remote can clobber unpushed submodule HEAD
type: lesson
---

# git submodule update --remote can clobber unpushed submodule HEAD

`git submodule update --remote <sub>` fetches the submodule's configured remote-tracking branch and checks the submodule out to **that branch tip** (e.g. `origin/main`) — not to whatever commit the submodule working tree currently holds.

So in a publish script, running it *after* you have locally advanced the submodule (a fresh commit, a rebase, or commits not yet pushed) can silently **move the pointer backward** to the old remote HEAD, discarding your new work before the superproject even records it.

**Fix / rule:** once the submodule working tree is already at the commit you want to publish, just `git add <sub>` in the superproject to record the checked-out SHA. Only use `--remote` when you explicitly want to *pull* the submodule up to its remote branch and you know local has nothing unpushed.

This bit the `obsidian-quartz` publish flow — see [[obsidian-quartz publishes via a content submodule pointer bump]] and the reconciliation technique in [[Classify local vs upstream with git merge-base to pick ff or rebase]].

## Related

- [[obsidian-quartz publishes via a content submodule pointer bump]]
- [[Classify local vs upstream with git merge-base to pick ff or rebase]]

%% ai-graph-start %%

**Related notes:**
- [[Classify local vs upstream with git merge-base to pick ff or rebase]]
- [[obsidian-quartz publishes via a content submodule pointer bump]]
- [[FETCH_HEAD is volatile when an IDE auto-fetches]]
- [[Publish]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]

%% ai-graph-end %%