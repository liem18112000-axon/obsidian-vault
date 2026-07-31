---
title: "Toggle a layout mode with one Tailwind descendant override instead of threading state"
created: 2026-07-23
type: howto
status: seedling
source: "session 2026-07-23"
tags: [tailwind, css, layout, vinnstack]
---

# Toggle a layout mode with one Tailwind descendant override instead of threading state

Adding a "full-width content" toggle to a view whose inner content carries a dozen scattered `max-w-3xl`/`max-w-5xl` caps (Vinnstack Interrogation Room, 2026-07-23): instead of threading a `wide` prop into every capped element (12 call sites, several child components), put ONE conditional class on the pane ROOT using Tailwind arbitrary variants:

```tsx
const paneClass = `min-h-0 flex-1 overflow-y-auto px-5 py-4${wide ? " [&_.max-w-3xl]:max-w-full [&_.max-w-5xl]:max-w-full" : ""}`;
```

`[&_.max-w-3xl]:max-w-full` compiles to `.parent .max-w-3xl { max-width: 100% }` — two-class specificity beats the single utility, so every descendant cap (including ones inside child components like VersionChanges/PrdComments) is neutralized from one place, and future capped elements are covered automatically.

Notes:
- Tailwind JIT picks the variant up because the full class string appears verbatim in source (keep it a literal, don't build it from pieces).
- Persist the preference in localStorage but READ it in a useEffect, not useState's initializer — Next pre-renders client components and a direct read causes a hydration mismatch.
- Identical class strings elsewhere in the file are a sed-by-line-number hazard: disambiguate by line, bottom-up, BEFORE anchor-based insertions shift the numbers.

**Correction (same day):** use `max-w-full`, NOT `max-w-none`. `none` removes the cap entirely, so inner content can overrun the pane's right padding and hit the panel edge (observed on the PRD tab). `full` = 100% of the padded parent — full-width AND the container's own `px-*` breathing room is preserved.

**Second correction (same feature):** the initial pane survey used `grep | head -5` and silently dropped the 5th occurrence of the shared pane class string — so the TRACKS pane (the feature's primary target!) never got the override, and the feature 'didn't work' for exactly the tabs the user cared about. When patching every occurrence of a repeated string, never truncate the survey (`grep -c` first, then list ALL), and verify by counting the replaced form afterwards.
