---
ai_hash: 90147b9be8401884
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack approve-with-open-comments guard
status: seedling
tags:
- ux
- react
- confirmation
- forms
title: Acknowledgment checkboxes must reset every time their dialog reopens
type: lesson
---

# Acknowledgment checkboxes must reset every time their dialog reopens

An "I understand the risk" checkbox that gates a confirm button is only a real guard if it is UNCHECKED every time the confirmation opens. If the flag lives in component state that survives the dialog closing (e.g. a conditional panel, not an unmounting modal), a user who acknowledged once gets a pre-checked box on every later approval - the friction the checkbox exists to create silently disappears after the first use.

Fix at the opener, not the closer: reset the flag in the same handler that shows the dialog (`onClick={() => { setAck(false); setConfirmOpen(true); }}`) - resetting on cancel paths misses re-entry routes; resetting on open covers all of them.

Middle rung of the friction ladder for risky actions: warn (text only) -> acknowledge (this pattern) -> hard block. Pick by how costly an accidental confirm is.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%