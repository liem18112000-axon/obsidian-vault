---
title: "Classify local vs upstream with git merge-base to pick ff or rebase"
created: 2026-07-31
type: howto
status: seedling
source: "session 2026-07-31 quartz vault sync"
tags: [git, scripting, merge-base, sync]
---

# Classify local vs upstream with git merge-base to pick ff or rebase

A sync/publish script must not decide "should I push?" from `git status` dirtiness alone — a **clean working tree can still hold unpushed commits**, and local and remote can *both* have moved (diverged). Classify the real relationship with `merge-base` and branch on it:

```bash
git fetch --quiet
LOCAL=$(git rev-parse HEAD)
REMOTE=$(git rev-parse @{u})
BASE=$(git merge-base HEAD @{u})

if   [[ $LOCAL == $REMOTE ]]; then :            # in sync — nothing to do
elif [[ $LOCAL == $BASE   ]]; then git pull --ff-only   # behind → fast-forward
elif [[ $REMOTE == $BASE  ]]; then git push            # ahead  → push
else git rebase @{u} && git push                        # diverged → rebase then push
fi
```

The four cases map to: equal SHAs = in sync; local == merge-base = strictly behind; remote == merge-base = strictly ahead; neither = diverged. The old `obsidian-quartz` publish.sh only ever handled behind (and only pushed when the tree was dirty), which is exactly why a clean-but-diverged vault got mis-handled.

Related: [[git submodule update --remote can clobber unpushed submodule HEAD]], [[obsidian-quartz publishes via a content submodule pointer bump]].

## Related

- [[git submodule update --remote can clobber unpushed submodule HEAD]]
- [[obsidian-quartz publishes via a content submodule pointer bump]]
