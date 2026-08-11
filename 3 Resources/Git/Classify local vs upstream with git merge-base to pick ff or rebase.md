---
ai_hash: 12548034c8d27630
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: session 2026-07-31 quartz vault sync
status: seedling
tags:
- git
- scripting
- merge-base
- sync
title: Classify local vs upstream with git merge-base to pick ff or rebase
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[git submodule update --remote can clobber unpushed submodule HEAD]]
- [[obsidian-quartz publishes via a content submodule pointer bump]]
- [[Publish]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]
- [[FETCH_HEAD is volatile when an IDE auto-fetches]]

%% ai-graph-end %%