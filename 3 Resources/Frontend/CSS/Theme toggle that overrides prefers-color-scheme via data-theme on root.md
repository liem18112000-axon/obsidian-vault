---
title: "Theme toggle that overrides prefers-color-scheme via data-theme on :root"
aliases: ["CSS dark mode that honors both OS preference and a manual toggle"]
created: 2026-07-14
type: howto
status: seedling
source: "session 2026-07-14 perf report HTML; accesstrade_integration web console 2026-06-12"
tags: [css, theming, dark-mode, html, frontend, htmx]
---

# Theme toggle that overrides prefers-color-scheme via data-theme on :root

A `@media (prefers-color-scheme)` block **alone gives no in-page control** — the page can only change when the OS setting changes. To cover all four (OS × user choice) combinations, layer a `data-theme` attribute on `<html>` over the media query so specificity resolves cleanly:

```css
:root { /* default palette */ }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) { /* dark tokens — auto-follows OS */ }
}
:root[data-theme="dark"]  { /* dark tokens — forced */ }
:root[data-theme="light"] { /* light tokens — forced */ }
```

- The `:not([data-theme…])` guard makes the media block stop applying the moment a manual choice exists, so forced-light wins on a dark OS.
- The standalone `[data-theme="dark"]` block is what lets a user force dark on a light OS — without it the media query wins.
- Dark tokens must be duplicated in both the media block and the attribute block (unavoidable without a preprocessor; keep them adjacent).

Toggle JS is just `document.documentElement.setAttribute("data-theme", …)`. Compute the *current effective* theme as
`getAttribute("data-theme") || (matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light")` so the first click flips relative to what the user is actually seeing.

Also:
- **Avoid FOUC:** read `localStorage` and set `data-theme` from an inline `<script>` in `<head>`, before the stylesheet paints — not in a deferred script or `DOMContentLoaded`.
- Add `<meta name="color-scheme" content="light dark">` so form controls and scrollbars theme too.
- Any color routed only through an `@media (prefers-color-scheme)` rule (e.g. a log/code box) is untouchable by the toggle — route it through the `:root[data-theme]` variables instead.
- Self-contained inline CSS+JS, so it works offline and under strict CSP (Claude Artifacts).
- If the toggle does a `location.reload()` (to rebuild charts/mermaid), the choice must be persisted first — see [[HTML report theme toggle reload gotcha]].

Applied in the luz-docs 800k perf-test report and the Accesstrade Console (FastAPI + Jinja + HTMX).

## Related

- [[HTML report theme toggle reload gotcha]]
- [[Inline SVG ignores theme unless shapes use CSS-variable classes, not hardcoded hex]]
- [[luz-docs folderIds facet 500 is a Mongo $group memory-limit abort (error 292)]]
