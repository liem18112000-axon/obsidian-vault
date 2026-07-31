---
ai_hash: bcf827ec7c603879
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack PRD inline-comments plan
status: seedling
tags:
- llm
- prompting
- review-loop
- vinnstack
title: Inject anchored inline comments into LLM regeneration prompts as quoted passages
type: model
---

# Inject anchored inline comments into LLM regeneration prompts as quoted passages

When an LLM regenerates a document that humans have inline-commented, give the model each comment **with its exact quoted anchor**, not just a bag of feedback. Shape:

```
=== INLINE REVIEW COMMENTS (each anchored to an exact passage) ===
[C-1] (section "## Solution") On the passage:
> "the queue is drained synchronously before ACK"
  - reviewer: contradicts B-3 - we committed to async drain
  - attachment: path/to/diagram.png  (readable via Read tool)

=== OVERALL REVIEWER FEEDBACK (applies to the whole document) ===
<free-text note, if any>

Instructions: revise so every comment is addressed at its exact passage; do not drop unrelated sections.
```

Key points: the block-quoted passage removes all ambiguity about what "this" refers to; the section heading gives coarse context if the quote moved; two channels coexist (anchored point comments + whole-document feedback); attachment file paths let an agent with filesystem access actually read referenced images/files. After regeneration, re-anchor comments against the new text and mark unmatched ones orphaned rather than resolved — only humans close their own comments.

## Related

- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-start %%

**Related notes:**
- [[TextQuoteSelector anchoring survives document regeneration]]
- [[LLM-implementable plan exports must bundle unresolved review state with precedence rules]]
- [[Version-stamp quality ratings so stale feedback stops driving regeneration]]
- [[AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run]]
- [[A feedback loop with only its write side wired looks like a broken feature]]

%% ai-graph-end %%