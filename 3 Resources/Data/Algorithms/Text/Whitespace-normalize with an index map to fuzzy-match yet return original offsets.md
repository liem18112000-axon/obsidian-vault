---
ai_hash: 287c0416f1322150
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack lib/textAnchor.ts
status: seedling
tags:
- text-matching
- anchoring
- typescript
title: Whitespace-normalize with an index map to fuzzy-match yet return original offsets
type: howto
---

# Whitespace-normalize with an index map to fuzzy-match yet return original offsets

When fuzzy-matching a quote against a document that may have been reflowed (newlines turned into spaces, whitespace runs collapsed), don't just `replace(/\s+/g, " ")` both sides — that finds the match but loses the ability to say *where* it is in the original text.

Instead, build the normalized string **together with an index map**: while emitting each kept character, push its original index into `map`, so `map[i]` = original position of `norm[i]`. After finding the match at normalized index `i` with length `L`, the original range is `start = map[i]`, `end = map[i + L - 1] + 1`.

Details that matter: drop *leading* whitespace during normalization (guard `norm.length > 0` before emitting the collapse space) and strip a possible trailing space from the normalized needle, so a quote and the document normalize consistently. Used in vinnstack `lib/textAnchor.ts` as tier 2 of anchor resolution, after exact match fails and before declaring the comment orphaned.

## Related

- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-start %%

**Related notes:**
- [[TextQuoteSelector anchoring survives document regeneration]]
- [[Map a DOM selection to plain-text offsets with a pre-range]]
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]

%% ai-graph-end %%