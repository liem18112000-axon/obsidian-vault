---
title: "Measure a broken-link baseline before a mass vault refactor"
created: 2026-07-31
type: lesson
status: seedling
source: "vault PARA reorganization 2026-07-31"
tags: [refactoring, verification, obsidian, git]
---

# Measure a broken-link baseline before a mass vault refactor

Before a refactor that moves or deletes a lot of notes, measure how many links are *already* broken, from a git worktree of the pre-change commit:

```bash
git worktree add /tmp/vaultbase <checkpoint-sha>
python linkcheck.py /tmp/vaultbase base.json
python linkcheck.py . now.json
```

Then diff the two sets of broken targets. Without the baseline you only see one number after the fact, and you cannot tell your own regressions from rot that predated you — so you either panic over inherited breakage or quietly ship real regressions inside the noise.

Concretely: this vault already had **765 broken links (442 distinct targets)** before I touched it. After a 1100-file reorganization the raw count looked alarming, but the set difference showed exactly 100 targets I had broken. Repairing those took the reorg-caused regressions to **zero**, and the same passes incidentally fixed **303** pre-existing ones.

The set difference is also the fix list — the "newly broken" targets are precisely what your rename map has to cover.

## Related

- [[Precompute and validate a file-level move map before a mass reorganization]]
- [[Obsidian plugin configs pin folders to exact vault-root paths]]
