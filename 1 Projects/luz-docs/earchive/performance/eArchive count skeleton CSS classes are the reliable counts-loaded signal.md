---
ai_hash: 80178f0a4fb59705
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- eArchive count skeleton CSS classes
- .folder-count-skeleton
- .section-title-count-skeleton
- eArchive count placeholders
- Playwright trace.zip DOM snapshot resources
- automation
- trace-earchive.js
- collectPageEnter done-check
- all folder-badge skeletons resolved
- eArchive company-root
- Trash tiles
- K Files badges
- exact badge-count matching
- quiet-period settling
- counts-loaded signal
- deterministic 'counts finished loading' signal
- grepping
source: trace DOM snapshot 2026-07-21
status: seedling
tags:
- earchive
- playwright
- technique
title: eArchive count skeleton CSS classes are the reliable counts-loaded signal
type: lesson
---

# eArchive count skeleton CSS classes are the reliable counts-loaded signal

eArchive count placeholders have dedicated CSS classes: `.folder-count-skeleton` (per-folder "K Files" badge) and `.section-title-count-skeleton` (the "Documents (N)" / "Custom (M)" heading counts). Found by grepping the Playwright trace.zip DOM snapshot resources for `skeleton` — no live DevTools session needed.

They make a deterministic "counts finished loading" signal for automation: a skeleton seen then gone means the count rendered; badge-less tiles (company root, Trash) never show one. Beats both exact badge-count matching (unreachable when some tiles never badge) and quiet-period settling (exits early — badges resolve up to ~40 s in while nothing else changes). Used in trace-earchive.js collectPageEnter done-check and reported as "all folder-badge skeletons resolved".

## Related

- [[eArchive company-root and Trash tiles never render K Files badges]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive company-root and Trash tiles never render K Files badges]]
- [[eArchive counter metrics timed from page-load start to skeleton replacement]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[Resettable MutationObserver harness measures skeleton-to-number appear time on SSR pages]]
- [[eArchive page DOM selectors (performance automation)]]

**Relations:**
- eArchive count skeleton CSS classes — *ARE_A_TYPE_OF* — counts-loaded signal
- eArchive count skeleton CSS classes — *ARE_RELIABLE_FOR* — counts-loaded signal
- .folder-count-skeleton — *IS_A* — eArchive count skeleton CSS classes
- .section-title-count-skeleton — *IS_A* — eArchive count skeleton CSS classes
- eArchive count placeholders — *HAVE_CLASS* — .folder-count-skeleton
- eArchive count placeholders — *HAVE_CLASS* — .section-title-count-skeleton
- .folder-count-skeleton — *IDENTIFIES* — per-folder 'K Files' badge
- .section-title-count-skeleton — *IDENTIFIES* — 'Documents (N)' / 'Custom (M)' heading counts
- eArchive count skeleton CSS classes — *DISCOVERED_VIA* — grepping
- grepping — *APPLIED_TO* — Playwright trace.zip DOM snapshot resources
- eArchive count skeleton CSS classes — *PROVIDE* — deterministic 'counts finished loading' signal
- deterministic 'counts finished loading' signal — *USED_FOR* — automation
- deterministic 'counts finished loading' signal — *USED_IN* — trace-earchive.js
- trace-earchive.js — *USES* — collectPageEnter done-check
- collectPageEnter done-check — *REPORTS* — all folder-badge skeletons resolved
- eArchive company-root — *NEVER_SHOWS* — eArchive count skeleton CSS classes
- Trash tiles — *NEVER_SHOWS* — eArchive count skeleton CSS classes
- eArchive count skeleton CSS classes — *IS_SUPERIOR_TO* — exact badge-count matching
- eArchive count skeleton CSS classes — *IS_SUPERIOR_TO* — quiet-period settling
- exact badge-count matching — *CHARACTERISTIC* — unreachable
- quiet-period settling — *CHARACTERISTIC* — exits early
- eArchive company-root — *NEVER_RENDERS* — K Files badges
- Trash tiles — *NEVER_RENDERS* — K Files badges

%% ai-graph-end %%