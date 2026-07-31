---
title: "Gesture-only features need an always-visible teacher - empty state must not hide the affordance"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23"
tags: [ux, discoverability, empty-state, vinnstack]
---

# Gesture-only features need an always-visible teacher - empty state must not hide the affordance

Vinnstack's inline comments (2026-07-23): the feature was fully functional, yet the operator reported "I cannot see HOW to add a comment" on the PRD/process-flow views. Root cause was pure discoverability, with a compounding empty-state bug:

- The only way to create a comment was a **hidden gesture** (select text → floating "Comment" pill). Nothing on screen taught it.
- The comments rail **auto-opened only when comments already existed** (`openCount > 0`) — so on a fresh document (or after the epic's data was purged) it stayed collapsed to a 40px strip.
- In the collapsed strip, the icon was **decorative, not clickable** — the sole click target was a 24px edge toggle.
- The one line of copy explaining the gesture lived INSIDE the open rail's empty state — visible only after the user already found the feature.

Lesson: a gesture-only affordance (text-selection, drag, long-press) is invisible by definition — it needs an always-visible teacher: open the panel by default so the empty state can explain the gesture, make every collapsed remnant a real button with the gesture in its tooltip, and write empty-state copy as instruction ("select any passage — a Comment button appears"), not as status ("no comments"). The cruel irony of gating help on content: the users who most need the teaching (zero comments yet) are exactly the ones who never see it.

Related: [[A feedback loop with only its write side wired looks like a broken feature]] — same feature, the other half of "comments don't work": one report, two independent causes (regen never read comments back; UI never taught how to write them).

## Related

- [[A feedback loop with only its write side wired looks like a broken feature]]
