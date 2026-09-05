---
title: "A git merge can silently revert a merged PR when two branches edit the same region"
created: 2026-09-05
type: lesson
status: seedling
source: "session 2026-09-05, leo-customer360 deploy-api.sh"
tags: [git, merge, conflict, gotcha, code-review]
---

# A git merge can silently revert a merged PR when two branches edit the same region

When two branches change the **same region** of a file and the branches are merged, resolving the conflict toward one side **discards the other side's edits for that region** — even if those edits were an already-merged, reviewed PR on the target branch. The reverted PR still shows as "merged" in its own history, so nothing flags that its changes vanished from the current tip. It only surfaces if you actually read the merged file.

**Where it bit (leo-customer360):** a local disk-reclaim change to `deploy-api.sh` was branched from the pre-PR base. Meanwhile PR #31 (temp env file -> `/opt/c360/.tmp` instead of `/tmp`) merged to main in the same `mktemp` region. The merge that combined them kept the reclaim side, silently reverting PR #31; the tip had `env_file=$(mktemp)` (default /tmp) again with no conflict marker left behind.

**How to catch / avoid it:**
- After a merge that touched a file another PR recently changed, diff the merge result against that PR: `git show <pr-merge>:path > /tmp/a; git show HEAD:path > /tmp/b; diff a b`, or `git log --oneline <base>..HEAD -- path` then read the file.
- Rebasing the feature branch onto latest main *before* it merges makes the same overlap show up as an explicit conflict you must resolve, instead of a silent auto-resolution.
- Treat "my branch was cut before that PR landed and touches the same lines" as a review trigger.

Related: [[SHA-pinned docker pulls accumulate and fill small deploy VM disks]].

## Related

- [[SHA-pinned docker pulls accumulate and fill small deploy VM disks]]
