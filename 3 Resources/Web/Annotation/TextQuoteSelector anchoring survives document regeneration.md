---
title: "TextQuoteSelector anchoring survives document regeneration"
created: 2026-07-03
type: concept
status: seedling
source: "session 2026-07-03, vinnstack PRD inline-comments plan"
tags: [annotation, anchoring, comments, vinnstack]
---

# TextQuoteSelector anchoring survives document regeneration

To anchor a comment to a passage of a document that will be rewritten (LLM-regenerated, human-edited), store the anchor as a W3C Web Annotation **TextQuoteSelector** — `{ quote, prefix (~64 chars), suffix (~64 chars), occurrence }` against the document's plain text — never as line numbers or character offsets.

Offsets die on the first edit; a quoted passage either re-resolves (exact match → quote-only by occurrence → whitespace-normalized fuzzy) or **fails detectably**, letting you mark the comment `orphaned` instead of silently pointing at the wrong text. This is exactly how Confluence inline comments degrade when the anchored text is edited away. Adding the nearest section heading to the anchor gives a coarse fallback for both re-anchoring and prompt context.

Resolution order: `prefix+quote+suffix` exact → `quote` by nth occurrence → fuzzy → null (orphan). Never auto-resolve an orphaned human comment — keep it visible until a human closes it.

## Related

- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
