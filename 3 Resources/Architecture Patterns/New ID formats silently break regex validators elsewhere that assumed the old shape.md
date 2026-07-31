---
title: "New ID formats silently break regex validators elsewhere that assumed the old shape"
created: 2026-07-09
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [data-modeling, regex, gotcha, validation]
---

# New ID formats silently break regex validators elsewhere that assumed the old shape

When you introduce a new ID format for an existing entity, grep for every regex/validator that pattern-matches the OLD format — they will silently reject the new one instead of erroring loudly, which is much harder to notice.

## Concrete case

In vinnstack, a Story's `key` used to always be a real Jira issue key (e.g. `LUZ-9`). A later change introduced local "draft" keys (`S1`, `S2`, …) for Stories not yet pushed to Jira. A comment-subject validator elsewhere in the codebase, `isCommentSubject` in `lib/interrogation/prdCommentsStore.ts`, had a regex `^flow:[A-Za-z][A-Za-z0-9_]*-\d+$` written when `flow:<key>` always meant a real Jira key — it requires a `LETTERS-DIGITS` shape. A subject of `flow:S1` (no dash, no trailing digit group) fails that regex and gets silently rejected wherever it's validated, breaking inline comments on any Process Flow for a draft Story with no error message pointing at the real cause.

## Why this is easy to miss

The validator lives in a completely different file/module than the one that introduced the new ID format, has no test coverage for the new shape (because it predates it), and fails "successfully" (returns false / rejects cleanly) rather than throwing — so nothing crashes, a feature just silently doesn't work for the new case. The fix is usually a quick regex broadening once found, but finding it requires deliberately searching for "who else assumes this ID looks like X" whenever X changes.

## Related
- [[Decouple internal PK from external ticket ID for draft-before-push records]]
