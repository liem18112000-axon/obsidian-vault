---
title: "PRD-parity checklist - what comment-driven regenerate with versions actually requires"
created: 2026-07-23
type: howto
status: seedling
source: "session 2026-07-23"
tags: [versioning, comments, regenerate, vinnstack]
---

# PRD-parity checklist - what comment-driven regenerate with versions actually requires

Giving an artifact "comments + regenerate + versions like the PRD" (done for Vinnstack's interrogation tracks, 2026-07-23) is a six-part parity checklist, not one feature:

1. **Version counter** — per-artifact INT, bumped at the moment of (re)generation (in the store's apply function, not the caller), self-migrated column + schema.
2. **Version-anchored comments** — pass the real version as `anchoredVersion` (a hardcoded 0 makes every comment look current forever).
3. **Version-tied critique/feedback** — the AI critic rates *a version*; stale detection (`fb.version !== version`) only works if generation passes the new version, not 0.
4. **Comment→prompt injection on regenerate** — the read side of the loop (see the write-only-feedback-loop lesson).
5. **Destructive-regen confirm** — unlike a PRD revision (text replaced, human input preserved as comments), regenerating a question set DELETES committed answers; the confirm must say exactly what's lost and what's carried (comments re-anchored).
6. **UI affordances** — version chip ("vN") + a Regenerate button whose label carries the open-comment count ("Regenerate (3 comments)") so the loop is visible.

Gotcha that motivated it: the regenerate button simply didn't exist for existing tracks — generation was only reachable at creation time, so the operator purged the whole DB to re-run. If an artifact can be regenerated at all, the regenerate control belongs on the artifact, always visible.

Related: [[A feedback loop with only its write side wired looks like a broken feature]], [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]

## Related

- [[A feedback loop with only its write side wired looks like a broken feature]]
