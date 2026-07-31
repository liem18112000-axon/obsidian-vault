---
title: "Grid/flex items default to min-height auto, blocking inner overflow scroll; add min-h-0"
created: 2026-07-19
type: lesson
status: seedling
source: "Vinnstack session 2026-07-19"
tags: [css, flexbox, grid, overflow, tailwind, gotcha]
---

# Grid/flex items default to min-height auto, blocking inner overflow scroll; add min-h-0

An inner scroll region (a child with flex-1 + overflow-y-auto) refuses to scroll — instead it grows and pushes siblings / overflows its container — when an ANCESTOR flex OR grid item is missing min-height:0. CSS defaults flex and grid items to min-height:auto, which lets them expand to fit content rather than shrink to the track/flex size, so overflow never activates.

Concrete Vinnstack case (2026-07): the Interrogation Room left inbox aside is a grid item (grid-cols-[300px_1fr], container h-[calc(100vh-2rem)]). Its inner epic list was flex-1 overflow-y-auto, but the aside grew to fit all epics and the list never scrolled. The sibling main had min-h-0; the aside did not. Fix = add min-h-0 to the aside. One class.

Rule: for a scroll region to work, EVERY ancestor from the scroll element up to the height-bounded container must have min-h-0 (Tailwind) / min-height:0 if it is a flex or grid item. Missing it on any link in the chain breaks the scroll. First thing to check when "overflow-y-auto isn't scrolling."
