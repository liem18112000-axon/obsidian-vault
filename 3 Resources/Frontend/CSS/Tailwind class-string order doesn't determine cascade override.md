---
ai_hash: 90c281fc53ceef62
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: vinnstack BDD Settings .env row editor bugfix, 2026-07-12
status: seedling
tags:
- tailwind
- css
- gotcha
- vinnstack
title: Tailwind class-string order doesn't determine cascade override
type: lesson
---

# Tailwind class-string order doesn't determine cascade override

Appending a Tailwind utility class later in a `className` string does NOT guarantee it overrides an earlier one in the same category (e.g. width, flex). Tailwind emits utilities into the compiled stylesheet in its own internal category/scale order, not in the order they appear in your JSX class string — so cascade precedence is decided by that generated CSS order, and two classes of equal specificity (single class selector) resolve by *stylesheet* position, not *source-string* position.

This bites you specifically when you build a className by concatenating a shared "base" class constant with a per-usage override, e.g. `${input} w-2/5` where `input` already bakes in `w-full`. You might expect `w-2/5` to win because it comes last in the string, but if Tailwings generated CSS happens to emit `.w-full` after `.w-2\/5` (order follows its internal width scale, not usage order), `w-full` wins instead — silently, with no warning.

Concrete case: components/bdd/BddSettings.tsx in the vinnstack repo had a flex row with a "key" input (`${input} w-2/5`, where `input` = "...w-full...") next to a "value" input (`${input} flex-1`). The key input's `w-full` beat the intended `w-2/5`, so it tried to occupy the entire row width, squeezing the value input down to near-zero — its typed text became invisible.

Fix: don't reuse a base class constant that already bakes in a conflicting utility for a spot where you need to override that same utility category. Instead, define a variant of the base without the conflicting utility (e.g. strip `w-full`) and let each call site supply its own width/flex utility explicitly. More generally: prefer composing width/flex/sizing utilities at the call site rather than baking them into a shared constant meant to be reused across differently-sized instances.

Related: this is a special case of a broader Tailwind rule — utilities of equal specificity are ordered by Tailwind's internal plugin/category order in the generated stylesheet, so "last class wins" only holds true *within* the same utility group when authored via Tailwind's own scale (e.g. two spacing values), and can break down across differently-invoked base+override compositions.

%% ai-graph-start %%

**Related notes:**
- [[Inline style width beats Tailwind breakpoint width classes]]
- [[Toggle a layout mode with one Tailwind descendant override instead of threading state]]
- [[Stack a rail-and-content row responsively with flex-col to lgflex-row]]
- [[Grid blowout - bare 1fr is minmax(auto,1fr) and intrinsic-width content can explode the column]]

%% ai-graph-end %%