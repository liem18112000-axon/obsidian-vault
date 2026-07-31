---
title: "Violence detection needs a trained classifier, not keyword lists"
created: 2026-07-20
type: lesson
status: seedling
source: "web research session 2026-07-20"
tags: [content-moderation, llm-safety, classification]
---

# Violence detection needs a trained classifier, not keyword lists

Keyword/regex lists are a poor primary signal for deciding whether a prompt contains violence: they are context-blind, so benign technical or creative phrasings ("kill a zombie process", "shooting a film") false-positive, while paraphrased or obfuscated threats slip through. Best practice is a trained classifier that scores the whole text per category, with a tunable threshold calibrated on labeled domain data (precision/recall trade-off), severity tiers instead of a binary flag, and human review for borderline scores.

A lexicon is acceptable only as a cheap pre-filter or as a last-resort fallback when no model can run.

## Related

- [[Moderation taxonomies split violence into subtypes]]
- [[Model options for detecting violent text by weight class]]
