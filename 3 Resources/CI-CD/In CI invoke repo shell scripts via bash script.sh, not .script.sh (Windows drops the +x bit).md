---
title: "In CI invoke repo shell scripts via bash script.sh, not ./script.sh (Windows drops the +x bit)"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20, cd.yml run 32366406386"
tags: [ci, shell, windows, git, gotcha, exit-126]
---

# In CI invoke repo shell scripts via bash script.sh, not ./script.sh (Windows drops the +x bit)

A shell script committed from a Windows working copy usually has **no git executable bit**, so on a Linux CI runner `./script.sh` fails with `Permission denied` and **exit code 126** (command found but not executable — distinct from 127 = not found).

Two fixes:
- **Invoke via the interpreter**: `bash script.sh args` — works regardless of the mode bit. Preferred in CI/workflows.
- **Set the bit in git** (persists cross-platform): `git update-index --chmod=+x script.sh` then commit, or `git add --chmod=+x`.

Real case: `cd.yml` ran `./deploy-all.sh …` and hit exit 126 on the GitHub runner because the deploy scripts were authored on Windows. Changed to `bash deploy-all.sh …`. Note `deploy-all.sh` already invoked its own sub-scripts as `bash "$path"`, so only the top-level `./` call needed fixing. Related: [[Chain a CD workflow after CI with workflow_run, gating on conclusion and ref]].

## Related

- [[Chain a CD workflow after CI with workflow_run]]
- [[gating on conclusion and ref]]
