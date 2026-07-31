---
title: "Stack a rail-and-content row responsively with flex-col to lg:flex-row"
created: 2026-07-09
type: howto
status: seedling
source: "vinnstack session 2026-07-09"
tags: [css, tailwind, flexbox, responsive-design]
---

# Stack a rail-and-content row responsively with flex-col to lg:flex-row

A side-by-side "rail beside content" layout becomes responsive with a single class swap on the flex parent: `flex flex-col gap-3 lg:flex-row lg:items-start`. Below the breakpoint the children stack vertically (rail above content, each naturally full-width); at `lg:` and up it becomes the original horizontal row with the rail sized by its own width classes and the content taking the rest via `flex-1`.

No grid is needed for this shape — grid only earns its keep when the rail's width should also define a fixed grid track (`grid grid-cols-1 lg:grid-cols-[300px_1fr]`), which is a distinct, coarser-grained idiom for a top-level two-pane app layout (inbox list + detail pane) rather than a small decorative rail sitting next to a document (self-assessment panel, comment thread rail). Use `flex-col`/`lg:flex-row` for the latter.

A `sticky top-0` rail also needs its stickiness scoped to the same breakpoint (`lg:sticky lg:top-0`) — sticky positioning on a rail that is stacked full-width above scrolling content does not make sense and can look broken, so keep it inert below the breakpoint.

Pairs with [[Inline style width beats Tailwind breakpoint width classes]] — the rail's own width still needs its inline style removed/converted for this parent-level fix to actually show up, since inline styles do not respect the parent's flex-direction change (a `w-full` state below `lg` was needed on the rail itself too). Applied in the vinnstack project to InterrogationView.tsx (PRD tab, Questions tab), StoryFlowView.tsx and PrdComments.tsx's own internal doc+comment-rail layout.

## Related

- [[Inline style width beats Tailwind breakpoint width classes]]
