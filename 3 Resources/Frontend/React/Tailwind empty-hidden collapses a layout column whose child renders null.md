---
ai_hash: b60470287b394b81
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack AiFeedback rail
status: seedling
tags:
- tailwind
- react
- layout
title: Tailwind empty-hidden collapses a layout column whose child renders null
type: howto
---

# Tailwind empty-hidden collapses a layout column whose child renders null

When a fixed-width flex column exists only to host a child component that may render `null` (e.g. a side rail that appears once data exists), put Tailwind's `empty:hidden` on the wrapper: `<div className="w-56 shrink-0 empty:hidden"><MaybeNull/></div>`. The CSS `:empty` pseudo-class hides the wrapper when the child rendered nothing, so the layout doesn't reserve a dead 14rem gutter - and you avoid lifting the child's "do I have data" state up into the parent purely for layout.

Caveat: `:empty` matches only when there are NO child nodes at all - whitespace text nodes or an empty fragment wrapper break it. Keep the wrapper's JSX to exactly the one component.

**Counterpoint (learned the hard way, same day):** collapse-when-empty is wrong for a NEWLY SHIPPED feature - the user reported "I don't see it" within hours, because an invisible empty state is indistinguishable from the feature not existing. For new or primary content, render a small placeholder card in the empty state saying what will appear and when ("No self-assessment yet - the model rates this after the next generation"). Reserve collapse-when-empty for established, secondary content whose absence needs no explanation.

%% ai-graph-start %%

**Related notes:**
- [[Inline style width beats Tailwind breakpoint width classes]]
- [[Grid blowout - bare 1fr is minmax(auto,1fr) and intrinsic-width content can explode the column]]
- [[Stack a rail-and-content row responsively with flex-col to lgflex-row]]
- [[Toggle a layout mode with one Tailwind descendant override instead of threading state]]

%% ai-graph-end %%