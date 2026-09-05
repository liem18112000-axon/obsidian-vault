---
title: "git checkout -B branch origin/main rehomes upstream to origin/main, so a bare push targets main"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20"
tags: [git, upstream, push, gotcha, force-with-lease]
---

# git checkout -B branch origin/main rehomes upstream to origin/main, so a bare push targets main

`git checkout -B <branch> origin/main` (and `git checkout -b <branch> origin/main`) **sets the new branchs upstream to `origin/main`**. A subsequent **bare `git push` then targets `main`**, not the feature branch — an easy way to accidentally push straight to main.

**Symptom:** after the checkout, git prints `branch <branch> set up to track origin/main`, and `git status` says `Your branch and origin/main have diverged`.

**Safe practice — always push the feature branch with an explicit refspec, and reset tracking:**
```
git push -u --force-with-lease origin <branch>      # explicit dst; -u rehomes upstream to origin/<branch>
```
After this, upstream is `origin/<branch>` again and bare `git push` is safe.

Prefer `--force-with-lease` over `--force`: it refuses the push if the remote branch advanced beyond your last-known remote-tracking ref (someone elses commit), so you only overwrite what you actually saw.

## Related

- [[A PR off a stale local main shows a huge misleading diff and CONFLICTING; use two-dot diff vs origin-main to find the real delta]]
