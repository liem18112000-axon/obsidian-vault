---
title: "Version-stamp quality ratings so stale feedback stops driving regeneration"
created: 2026-07-03
type: model
status: seedling
source: "session 2026-07-03, vinnstack artifact feedback"
tags: [llm, feedback-loop, regeneration, vinnstack]
---

# Version-stamp quality ratings so stale feedback stops driving regeneration

When a persistent quality rating (e.g. 5-star + summary) feeds an LLM regeneration loop, stamp it with the ARTIFACT VERSION it rated and inject it only while that version is still current. Unlike inline comments - which re-anchor and orphan when their passage disappears - a bare rating has no lifecycle: without the stamp, one "2/5, too shallow" would be re-injected into every future run forever, even after the complaint was fixed, biasing the model against its own improvements.

Rule: `inject if feedback.version === artifact.version` (the rating criticizes exactly the text being revised). After regeneration bumps the version, the stale rating is display-only until the reviewer re-rates. For artifacts without a version counter, either add one or accept always-inject with the staleness risk documented.

This is the ratings-counterpart of the comment lifecycle rule (orphan, never auto-resolve): every piece of feedback that enters a regeneration prompt needs an explicit answer to "when does this stop applying?".

## Related

- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
