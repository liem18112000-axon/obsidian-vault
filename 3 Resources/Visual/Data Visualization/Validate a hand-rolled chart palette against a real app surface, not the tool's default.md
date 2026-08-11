---
ai_hash: e181b564e14b7390
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: vinnstack session 2026-07-09
status: seedling
tags:
- dataviz
- color
- accessibility
- charts
title: Validate a hand-rolled chart palette against a real app surface, not the tool's
  default
type: lesson
---

# Validate a hand-rolled chart palette against a real app surface, not the tool's default

When building charts for an app that has scattered, ad-hoc per-feature accent colors (not a coordinated categorical palette) and no charting library, don't try to reuse those existing accents directly as chart series colors — validate a purpose-built categorical set instead, then map it onto the app's categories in an order that loosely echoes existing associations.

## Why not just reuse existing accents

Per-feature accent colors (e.g. one color for a "PRD" feature, another for "Process Flow") are usually chosen individually for that one feature's buttons/badges, not as a coordinated set — stacking 5-8 of them together in one chart has no guarantee of passing colorblind-safety (CVD) or contrast checks as a *set*, since nobody validated their pairwise relationships. A validated categorical palette (checked for CVD separation and contrast) is built exactly for standing side-by-side; ad hoc accents are not.

## The workflow that worked

1. Get the app's REAL chart-surface colors (not a generic default) — e.g. `grep` the CSS custom properties for the light/dark card background actually used.
2. Take a pre-validated categorical hue set (Anthropic's dataviz skill ships one: blue/aqua/yellow/green/violet/red/magenta/orange, both light and dark steps).
3. Assign each of the app's categories to one hue slot, picking the slot whose "vibe" already loosely matches that category's existing usage where possible (e.g. a category that already uses a yellowish accent elsewhere gets the yellow slot) — this keeps the new chart feeling coherent with the rest of the app without needing the exact same hex.
4. Run the actual validator script against the REAL surface colors from step 1 for both light and dark mode before shipping: `node validate_palette.js "<hex,hex,...>" --mode light --surface <real-light-hex>` and again with `--mode dark --surface <real-dark-hex>`. A PASS with a contrast WARN on some slots just means: never rely on those slots' color alone — always pair with a direct text label (which a well-designed chart should have anyway).

## Related
- [[Decouple internal PK from external ticket ID for draft-before-push records]]

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%