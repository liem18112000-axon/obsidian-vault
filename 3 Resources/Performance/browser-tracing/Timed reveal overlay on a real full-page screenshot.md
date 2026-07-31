---
tags: [technique, dataviz, playwright, performance, html]
created: 2026-07-18
---

# Timed reveal overlay on a real full-page screenshot

To replay real render timings *on the actual UI* (not synthetic mock cards): capture a full-page screenshot, then drive a timed overlay on top of it.

**Steps**
1. Playwright `browser_take_screenshot { fullPage:true, filename }` → real PNG of the rendered page.
2. Before/after, grab region geometry via `getBoundingClientRect` **normalized to the full page** (`(rect.left+scrollX)/scrollWidth`, etc.) so the boxes are resolution-independent `%` coordinates. Use a union bbox over a node set (e.g. all `.letter-wrapper`) for the "documents grid" region; a single `.folders-container` rect for the folder strip.
3. In the HTML, reference the PNG as a **relative `<img src>`** when it ships in the same folder — keeps the HTML tiny (20 KB) vs. base64-embedding (~+600 KB). Base64 only if the file must travel alone.
4. Overlay, absolutely positioned over the img: a full-cover **veil** (spinner) that gets `.clear` (opacity 0) at the content mark, and per-region **highlight boxes** (positioned from the `%` rects) that toggle `.on` at their own marks. A `requestAnimationFrame` loop maps `virtualTime = (now-start)*speed` to the milestone thresholds.

**Why it reads honestly:** the veil clearing at the measured content-ms shows exactly when the *real* page became usable; the stalled-trial replay spins the veil over the true UI for its full 28.7 s, which a synthetic mock can't convey as credibly.

**Gotcha — fullPage vs viewport shot.** A `fullPage:true` screenshot of a long page (e.g. 1366×4741) becomes a thin unusable sliver once you cap it with `max-height:Nvh` (aspect ratio ~0.29 → ~150px wide). For a replay that must *fit one screen*, take a **viewport** screenshot (`fullPage:false`) of the above-the-fold region instead — it's landscape and scales cleanly. Then measure region rects **relative to the viewport** (`innerWidth`/`innerHeight`, clamp to [0,100]%), not the full scroll height, so the overlays line up.

**Gotcha — session modal pollutes the shot.** An idle klara tab shows a "session has been terminated" dialog; the screenshot silently captures it over the UI. Before shooting, click any visible "Continue my session" and assert the modal has no *visible* node (`offsetParent!==null` + height>40) — text-in-DOM alone is a hidden template and gives a false positive.

**Gotcha — overlay labels floating above a region collide with the screenshot text.** A region-highlight label placed *above* its box (`top:-21px`) sits over the underlying UI text baked into the screenshot and reads as garbled/"broken" text. Put the label **inside** the box top-left instead, as an **opaque dark pill** (`rgba(17,21,28,.92)`, white text) with a small colored dot for the region — readable over any box fill, no collision. A semi-transparent or accent-colored pill can vanish against a same-hue box fill; go opaque + dot.

Applied in `luz_docs/docs/performance-test-800k/end-to-end-video/journey-report.html`.

Related: [[Measure component render timing with Playwright addInitScript]] · [[Perf 800k tenant eArchive reload timing]]
