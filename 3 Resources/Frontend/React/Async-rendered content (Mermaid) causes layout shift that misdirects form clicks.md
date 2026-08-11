---
ai_hash: b2c2db213a9011e8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack Interrogation Room technical screen)
status: seedling
tags:
- react
- cls
- layout-shift
- mermaid
- forms
- gotcha
- vinnstack
- playwright
title: Async-rendered content (Mermaid) causes layout shift that misdirects form clicks
type: lesson
---

# Async-rendered content (Mermaid) causes layout shift that misdirects form clicks

**Symptom:** a form works fine except on one screen, where clicks seem to "do the wrong thing" or an answer "won't save."

**Cause:** content that renders **asynchronously** and **changes height after paint** — a lazily-loaded Mermaid/chart/image without reserved dimensions — pushes everything below it down (cumulative layout shift, CLS). If the user is clicking or typing during that reflow, the control they aimed at moves and the click lands on a *different* control. In Vinnstack's Interrogation Room this made a "Save answer" click hit an adjacent question's option button; selecting an option runs onAnswer(q, optionId, null) which **wipes the free text**, so the typed answer looked impossible to save. Only the technical screen was affected — it has Mermaid diagrams; the business screen has none.

**Tell-tale:** the wrongly-changed control is always the *recommended/default* one, or a *neighbouring* item — not random. And it only bites during the first 1-2s while heavy content lazy-loads.

**Fix:** reserve stable space so the async swap doesn't reflow. Give the container a **fixed height** (or matching min/max-height) with overflow:auto in BOTH the placeholder and rendered states, so the pre->SVG swap is zero-shift. Scope it with a prop (e.g. stableHeight) so only the form context pays the constraint. A click-to-enlarge/lightbox recovers full detail from the now-scrollable box.

**Debugging technique:** drive the real UI with Playwright, click one control, then read the *server* state — if a control you did NOT touch changed, it's a misdirected interaction (layout/overlay), not a backend bug. Measure box heights via browser_evaluate to confirm they're now constant.

%% ai-graph-start %%

**Related notes:**
- [[Debug UI overflow by headless reproduction with DOM overflow diagnostics, not blind CSS guesses]]
- [[Cached-rejected lazy import silently breaks a feature for the whole session]]

%% ai-graph-end %%