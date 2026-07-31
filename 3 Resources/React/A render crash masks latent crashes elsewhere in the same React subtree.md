---
title: "A render crash masks latent crashes elsewhere in the same React subtree"
created: 2026-07-26
type: lesson
status: seedling
source: "vinnstack cloud-build fix, session 2026-07-26"
tags: [react, debugging, gotcha]
---

# A render crash masks latent crashes elsewhere in the same React subtree

When any component throws during render, React unwinds and aborts rendering the entire subtree (absent an error boundary). Consequently, one render-time crash MASKS every latent crash in its sibling and child components — they never got the chance to render and throw.

Practical consequence for debugging: a CI/test log that reports a single render crash may be hiding more. After fixing the first crash, always RE-RUN — the fix lets the render proceed further and can surface an identical bug downstream. And when the crash is a pattern (e.g. `undefined.length` from an unguarded fetch assignment), grep the whole codebase for the pattern instead of fixing only the one the log named.

Real case (vinnstack, 2026-07): the CI log showed only `MdExportControl` crashing at `versions.length`. Fixing it exposed a byte-for-byte identical crash in `VersionChanges` that the log had never mentioned.

See also [[Guard array-typed React state seeded from a fetch with ?? []]].

## Related

- [[Guard array-typed React state seeded from a fetch with ?? []]]
