---
title: "Debug UI overflow by headless reproduction with DOM overflow diagnostics, not blind CSS guesses"
created: 2026-07-23
type: howto
status: seedling
source: "session 2026-07-23"
tags: [playwright, debugging, css, ui]
---

# Debug UI overflow by headless reproduction with DOM overflow diagnostics, not blind CSS guesses

When a user reports 'the UI overflows/looks broken' and code reading yields only guesses, REPRODUCE headlessly instead of iterating blind fixes (two wrong guesses preceded the real one in Vinnstack's wide-mode overflow):

1. Borrow Playwright from any sibling repo's node_modules via `createRequire(<that repo>/package.json)` — no install needed in the target repo.
2. Drive the running localhost app to the exact view (exact-text selectors; ASSERT the destination content is visible before measuring — a missed click silently measures the wrong page, which happened on the first pass after a hot reload reset the view).
3. Screenshot at each navigation step (cheap breadcrumbs when a selector misses) and Read the PNG to actually SEE the state.
4. Dump DOM overflow diagnostics in page.evaluate: every element with `scrollWidth > clientWidth` + overflow-x visible, and every bounding rect extending past the viewport. The widest offender with visible overflow is the culprit; its class string names the exact source line.
5. Re-run the same script after the fix — before/after numbers (3276→contained) beat 'looks right to me'.

Caveat: rect-past-viewport alone is NOT proof of visible overflow — content inside an overflow-auto box legitimately reports rects beyond the viewport (it's clipped and scrollable). Pair rect checks with the overflow-x:visible filter.

## Related

- [[3 Resources/Frontend/CSS/Grid blowout - bare 1fr is minmax(auto,1fr) and intrinsic-width content can explode the column]]
