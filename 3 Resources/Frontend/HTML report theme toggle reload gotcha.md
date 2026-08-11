---
ai_hash: 824df94ec1908f25
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
tags:
- html
- report
- theme
- dark-mode
- gotcha
title: HTML report theme toggle reload gotcha
---

# HTML report theme toggle reload gotcha

Recurring bug in the self-contained HTML perf reports: the ◐ Theme button "doesn't work".

## Cause
The toggle handler flips a JS `let dark` then calls `location.reload()` (needed so Chart.js/mermaid re-read CSS vars for the new theme). But on reload `dark` re-initialises from `matchMedia(prefers-color-scheme)` — the click is discarded. Net effect: button appears dead.

## Fix
Persist the choice, read it on load:
```js
const saved = localStorage.getItem("report-theme");
let dark = saved ? saved === "dark" : mq.matches;
function applyTheme(){ root.setAttribute("data-theme", dark ? "dark" : "light"); }
applyTheme();
btn.addEventListener("click", () => {
  dark = !dark;
  localStorage.setItem("report-theme", dark ? "dark" : "light");
  location.reload();   // charts/mermaid rebuild from CSS vars under the new [data-theme]
});
```
Any reload-based theme toggle MUST persist to localStorage (or a cookie) or the state is lost on the reload it triggers.

## Related

- [[3 Resources/Frontend/CSS/Theme toggle that overrides prefers-color-scheme via data-theme on root]] — the CSS layering this toggle drives

%% ai-graph-start %%

**Related notes:**
- [[Theme toggle that overrides prefers-color-scheme via data-theme on root]]
- [[Inline SVG ignores theme unless shapes use CSS-variable classes, not hardcoded hex]]
- [[Theme shared overlays with CSS-variable-backed Tailwind classes, not hardcoded colors]]

%% ai-graph-end %%