---
ai_hash: 805c8337bb8b2782
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack PRD inline-comments plan
status: seedling
tags:
- annotation
- anchoring
- comments
- vinnstack
title: TextQuoteSelector anchoring survives document regeneration
type: concept
---

# TextQuoteSelector anchoring survives document regeneration

To anchor a comment to a passage of a document that will be rewritten (LLM-regenerated, human-edited), store the anchor as a W3C Web Annotation **TextQuoteSelector** — `{ quote, prefix (~64 chars), suffix (~64 chars), occurrence }` against the document's plain text — never as line numbers or character offsets.

Offsets die on the first edit; a quoted passage either re-resolves (exact match → quote-only by occurrence → whitespace-normalized fuzzy) or **fails detectably**, letting you mark the comment `orphaned` instead of silently pointing at the wrong text. This is exactly how Confluence inline comments degrade when the anchored text is edited away. Adding the nearest section heading to the anchor gives a coarse fallback for both re-anchoring and prompt context.

Resolution order: `prefix+quote+suffix` exact → `quote` by nth occurrence → fuzzy → null (orphan). Never auto-resolve an orphaned human comment — keep it visible until a human closes it.

## Related

- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]

%% ai-graph-start %%

**Related notes:**
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
- [[Map a DOM selection to plain-text offsets with a pre-range]]
- [[Version-stamp quality ratings so stale feedback stops driving regeneration]]
- [[Whitespace-normalize with an index map to fuzzy-match yet return original offsets]]
- [[CSS Custom Highlight API paints text ranges without DOM mutation]]

%% ai-graph-end %%