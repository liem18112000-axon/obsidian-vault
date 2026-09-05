---
title: "set -u trips on a bash array only populated on the failure path, making success exit non-zero"
created: 2026-08-20
type: gotcha
status: seedling
source: "leo-customer360 deploy-all.sh, 2026-08"
tags: [bash, set-u, arrays, exit-code, ci, gotcha]
---

# set -u trips on a bash array only populated on the failure path, making success exit non-zero

A bash array that is only ever appended-to on a conditional branch (classic: `FAIL_STEPS+=(...)` only when a step fails) is UNBOUND on the branch where that condition never fires. Under `set -u`, an end-of-run reference like `${#FAIL_STEPS[@]}` in the summary then throws `unbound variable` — on the SUCCESS path — so the script exits non-zero even though everything worked. Nasty because it only bites when nothing went wrong (the happy path is the least-tested), and CI/automation reads the non-zero code as a failed deploy.

**Fix:** initialize the array empty at the top, next to the other vars: `OK_STEPS=(); FAIL_STEPS=()`. (Or guard each use: `${arr[@]:-}` / `${#arr[@]:-0}`, but eager init is cleaner and covers every reference.) Same trap applies to any var conditionally set then unconditionally read under `set -u`.

Related: [[Apostrophe inside bash ${var:?message} breaks the parser]]

## Related

- [[Apostrophe inside bash ${var:?message} breaks the parser]]
