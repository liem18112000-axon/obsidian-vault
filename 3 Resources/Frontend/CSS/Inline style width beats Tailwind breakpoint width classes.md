---
ai_hash: b0bcc60895e0fbd7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-09
entities: []
source: vinnstack session 2026-07-09
status: seedling
tags:
- css
- tailwind
- specificity
- react
- responsive-design
- gotcha
title: Inline style width beats Tailwind breakpoint width classes
type: lesson
---

# Inline style width beats Tailwind breakpoint width classes

An inline `style={{width: ...}}` prop on a React element always overrides a Tailwind breakpoint class like `lg:w-56` targeting the same `width` property, because inline styles carry higher CSS specificity than any class selector — Tailwind cannot win this fight no matter which breakpoint variant is used.

This bites components that size themselves via JS state rather than pure CSS: e.g. a collapsible side panel that picks its pixel width from `open` state (`style={{width: open ? 224 : 40}}`). Adding `lg:w-56`/`lg:w-10` classes to try to make it responsive silently does nothing below or above the breakpoint — the inline style always wins, so the panel stays a fixed pixel width at every viewport size. There is no console warning or type error; the layout just looks wrong at narrow widths, which makes this easy to miss during a quick review of the JSX.

The fix is to drop the inline style entirely and encode every state as a Tailwind class picked by a ternary inside a template literal:

```jsx
className={`relative w-full overflow-hidden lg:shrink-0 ${open ? "lg:w-56" : "lg:w-10"}`}
```

`w-full` supplies the width below the breakpoint (lets it fill its container when the layout is stacked), and the `lg:` variants restore the old fixed pixel width only at that breakpoint and up. This composes cleanly when the original pixel values land exactly on Tailwind's 4px spacing scale (56*4=224px, 10*4=40px, 72*4=288px) — otherwise fall back to arbitrary-value classes like `lg:w-[224px]`.

Found and fixed in the vinnstack project (AiFeedback.tsx, PrdComments.tsx comment rail) while making a desktop-only layout degrade to tablet widths — verified with Playwright screenshots at 768/900/1280px that the fixed-width panels then correctly went full-width below `lg` and back to their pixel width above it.

## Related
[[3 Resources/Frontend/CSS/Stack a rail-and-content row responsively with flex-col to lgflex-row]]

## Related

- [[3 Resources/Frontend/CSS/Stack a rail-and-content row responsively with flex-col to lgflex-row]]

%% ai-graph-start %%

**Related notes:**
- [[Stack a rail-and-content row responsively with flex-col to lgflex-row]]
- [[Toggle a layout mode with one Tailwind descendant override instead of threading state]]
- [[Tailwind class-string order doesn't determine cascade override]]
- [[Grid blowout - bare 1fr is minmax(auto,1fr) and intrinsic-width content can explode the column]]
- [[Tailwind empty-hidden collapses a layout column whose child renders null]]

%% ai-graph-end %%