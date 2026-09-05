---
title: "Confirm the deployed artifact contains the fix before judging an env test"
created: 2026-08-19
type: lesson
status: seedling
source: "luz-docs-import v2+v3 matrix run 2026-08-19"
tags: [deployment, verification, git, gotcha]
---

# Confirm the deployed artifact contains the fix before judging an env test

Before concluding that a fix "passes" or "fails" in a deployed environment, first prove the **running artifact actually contains the fix** — otherwise you may be testing old code and mislabel a deployment gap as a code regression (or vice-versa).

**Procedure.** Map the live image tag → git commit SHA → `git grep <marker>` in that SHA for something unique to the fix (a new constant, method, or string), and check ancestry (`git merge-base --is-ancestor <sha> HEAD`).

**Concrete case.** Running the v3 (LUZ-158230 folder-fix) test cases on dev-staging showed the pre-fix root-orphan bug. The dev-staging `luz-docs-import` image `d4c18dc2…` resolved to branch `hotfix/0.00.14.01`, and `git grep DETAIL_PARENT_FOLDER_FAILED` (a constant introduced by the fix) returned **nothing** for that commit — the hotfix was not an ancestor of the fix branch. So the "failure" was simply that the fix had never been deployed there; the same cases pass on `dev` where it is deployed.

**Takeaway.** An environment is only a valid test of a change once you have verified the change is present in the artifact that environment is running. "It failed on env X" is meaningless without "env X runs commit Y, which contains the change."

## Related

- [[Verify invariants at the source of truth]]
- [[not the operation's success report]]
