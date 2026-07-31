---
title: "CSS dark mode that honors both OS preference and a manual toggle"
created: 2026-06-12
type: howto
status: seedling
source: "session 2026-06-12, accesstrade_integration web console"
tags: [css, frontend, dark-mode, theming, htmx]
---

# CSS dark mode that honors both OS preference and a manual toggle

**To support dark mode that follows the OS by default *and* lets the user override it, gate the auto rule with `:root:not([data-theme])` and add an explicit `[data-theme="dark"]` block — then a tiny script sets/clears `data-theme` on `<html>`.** This is the cleanest way to get three states (auto / forced-light / forced-dark) with plain CSS variables and no framework.

```css
:root { /* light tokens */ }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) { /* dark tokens — auto */ }
}
[data-theme="dark"] { /* dark tokens — forced */ }
```

Key points:
- The media block uses `:not([data-theme="light"])` so a user who forces light still gets light even on a dark OS. The standalone `[data-theme="dark"]` block forces dark on a light OS. Together they cover all four (OS × choice) combinations.
- Put the dark tokens in BOTH the media query and the attribute block (duplication is unavoidable without a preprocessor; keep them adjacent).
- **Avoid the flash of wrong theme (FOUC):** read `localStorage` and set `document.documentElement.setAttribute('data-theme', ...)` in a `<script>` in `<head>` BEFORE the stylesheet/paint — not in a deferred script or DOMContentLoaded handler.
- Add `<meta name="color-scheme" content="light dark">` so form controls/scrollbars themable too.
- The toggle resolves "current" from the attribute first, falling back to `matchMedia('(prefers-color-scheme: dark)')`, then writes the opposite to both the attribute and localStorage.

Built for the Accesstrade Console frontend (FastAPI + Jinja + HTMX, vendored single CSS file). Also used `label:has(> input[type=checkbox])` to fix checkbox label layout without extra classes.
