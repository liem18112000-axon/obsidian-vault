---
title: "Nested interactive elements break accessible-name queries in tests"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session 2026-07-08"
tags: [accessibility, react, testing-library, html, gotcha]
---

# Nested interactive elements break accessible-name queries in tests

Putting one interactive control (a `<button>`, or a `<span role="button">`) inside another interactive control is invalid — nested interactive content is disallowed by the HTML spec and confuses assistive tech — and it also breaks React Testing Library's `getByRole("button", { name: /.../ })` queries by making two elements match the same accessible-name pattern.

## Concrete failure

A tab-style button rendered a Story's key as its label (e.g. "LUZ-10") and, to add a delete affordance, nested a `<span role="button" title="Delete Story LUZ-10">` inside it. Testing Library's accessible-name computation falls back to the `title` attribute when there's no `aria-label`/`aria-labelledby` and no usable text content (the icon inside was `aria-hidden`), so the inner span's computed name became "Delete Story LUZ-10" — which also matches a regex like `/LUZ-10/`. `getByRole("button", { name: /LUZ-10/ })` then throws "multiple elements found" because BOTH the outer tab button (whose name includes the visible "LUZ-10" text) and the inner delete span match.

## Fix

Don't nest interactive elements. Restructure as siblings: a wrapping non-interactive container (e.g. a `<span>` or `<div>`) holding two separate `<button>`s side by side — one for the primary action (select the tab), one for the secondary action (delete). This is the standard pattern for "tab with close button" UIs (browser tabs, editor tabs) and avoids the accessible-name collision entirely, since each button now has its own independent, non-overlapping accessible name.

## Related
- [[Decouple internal PK from external ticket ID for draft-before-push records]]
