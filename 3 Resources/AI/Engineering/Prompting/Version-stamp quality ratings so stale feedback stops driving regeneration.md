---
ai_hash: d44911154ebac130
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack artifact feedback
status: seedling
tags:
- llm
- feedback-loop
- regeneration
- vinnstack
title: Version-stamp quality ratings so stale feedback stops driving regeneration
type: model
---

# Version-stamp quality ratings so stale feedback stops driving regeneration

When a persistent quality rating (e.g. 5-star + summary) feeds an LLM regeneration loop, stamp it with the ARTIFACT VERSION it rated and inject it only while that version is still current. Unlike inline comments - which re-anchor and orphan when their passage disappears - a bare rating has no lifecycle: without the stamp, one "2/5, too shallow" would be re-injected into every future run forever, even after the complaint was fixed, biasing the model against its own improvements.

Rule: `inject if feedback.version === artifact.version` (the rating criticizes exactly the text being revised). After regeneration bumps the version, the stale rating is display-only until the reviewer re-rates. For artifacts without a version counter, either add one or accept always-inject with the staleness risk documented.

This is the ratings-counterpart of the comment lifecycle rule (orphan, never auto-resolve): every piece of feedback that enters a regeneration prompt needs an explicit answer to "when does this stop applying?".

## Related

- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]

%% ai-graph-start %%

**Related notes:**
- [[AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run]]
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
- [[LLM-implementable plan exports must bundle unresolved review state with precedence rules]]
- [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]
- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-end %%