---
title: "Theme toggle that overrides prefers-color-scheme via data-theme on :root"
created: 2026-07-14
type: howto
status: seedling
source: "session 2026-07-14 perf report HTML"
tags: [css, theming, dark-mode, html, frontend]
---

# Theme toggle that overrides prefers-color-scheme via data-theme on :root

To give a theme-aware page BOTH automatic OS-following AND a manual override button, layer the CSS so specificity resolves cleanly:

1. `:root { ... }` holds the **default (dark)** custom-property palette.
2. `@media (prefers-color-scheme: light) { :root:not([data-theme]) { ...light... } }` — auto-follows the OS, but the `:not([data-theme])` guard means it stops applying the moment a manual choice exists.
3. `:root[data-theme="light"] { ...light... }` and `:root[data-theme="dark"] { ...dark... }` — the manual override. The explicit `[data-theme="dark"]` block is needed so a user can force dark even on an OS set to light (otherwise the media query would win).

The button JS just toggles `document.documentElement.setAttribute("data-theme", ...)`. Compute the *current effective* theme as `getAttribute("data-theme") || (matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light")` so the first click flips correctly relative to what the user is actually seeing.

Gotcha: a plain `@media (prefers-color-scheme)` block alone gives you NO in-page control — the page can only change when the OS/browser setting changes. The `data-theme` attribute layer is what makes a toggle possible. All self-contained (inline CSS+JS), so it works offline and in strict-CSP contexts like Claude Artifacts.

Applied in the luz-docs 800k perf-test report (see [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]).

## Related

- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
