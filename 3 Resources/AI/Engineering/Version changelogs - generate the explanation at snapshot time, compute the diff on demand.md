---
ai_hash: c356aef0cf0d5424
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- versioning
- diff
- changelog
- llm
- vinnstack
title: Version changelogs - generate the explanation at snapshot time, compute the
  diff on demand
type: model
---

# Version changelogs - generate the explanation at snapshot time, compute the diff on demand

Adding "compare versions + short explanation" to versioned artifacts (Vinnstack md_exports, 2026-07-23) splits cleanly by WHEN each piece is cheap:

- **Explanation → generate at snapshot time, asynchronously.** The moment a new version lands is the only moment both texts are naturally in hand and the trigger (submit/regenerate) is known. Fire-and-forget a cheap tool-less LLM call (effort `low` via the per-stage map) whose input is the LINE DIFF (capped ~12k chars), not both full documents — smaller, and forces the summary to be about the delta. Store on the version row (`explanation TEXT`); UI shows "being written… refresh shortly" until it arrives. Failures just leave NULL.
- **Diff → compute on demand, server-side.** Don't store diffs (derivable) and don't ship both documents to the client. A dependency-free LCS line diff (~40 lines, O(n·m) fine for few-hundred-line docs) with context collapsed to 2 lines around changes ('⋯ N unchanged lines ⋯') renders directly as colored <pre> lines (+ green, - red).
- **UI: collapsed-by-default strip** whose collapsed state still shows the latest explanation truncated inline ("Changes · v2 → v3 — <summary>…") — the information is glanceable without the expansion, satisfying "default minimized" without hiding the value.

Gotcha hit: adding a new LLM call site means extending the typed `UsageKind` union (usage telemetry) and the effort map — grep for the unions when introducing a new run kind.

Related: [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]], [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]

## Related

- [[3 Resources/Practices/Architecture/Version artifacts by lifecycle event with content-dedupe, store in DB not files]]

%% ai-graph-start %%

**Related notes:**
- [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]
- [[Async-enriched columns need a lazy backfill for pre-feature rows]]
- [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]
- [[Comparison narratives invent deltas - anchor every claimed change to the machine diff]]
- [[LLM-implementable plan exports must bundle unresolved review state with precedence rules]]

%% ai-graph-end %%