---
ai_hash: 3c4742601864e63d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack requireSession helper
status: seedling
tags:
- typescript
- narrowing
- unions
title: Inferred union returns grow optional-undefined members - annotate to keep in-narrowing
type: lesson
---

# Inferred union returns grow optional-undefined members - annotate to keep in-narrowing

A helper that returns different object literals per branch - `return { error: resp }` vs `return { id, email }` - gets an INFERRED union where TypeScript normalizes each member with the other member's keys as optional-undefined: `{ error: R; id?: undefined } | { error?: undefined; id: string }`. Consequence: `if ("error" in s)` no longer excludes the success member (the key exists optionally on it), so `s.error` types as `R | undefined` and every caller sees "possibly undefined" - with the errors surfacing far away, in CALLER files, not the helper.

Fix: annotate the helper's return type with the exact discriminated union (`Promise<{ error: X } | { id: string; email: string }>`); the annotation prevents the optional-key normalization and `in`-narrowing works again.

Debug technique that found it: force explicit return annotations on the CALLERS (`: Promise<NextResponse>`) - the mysterious downstream "possibly undefined" then re-materializes as a precise TS2322 at the actual leaking expression.

## Related

- [[Array.every on an empty array is true - gate on existence before completeness]]

%% ai-graph-start %%

**Related notes:**
- [[Validate fetch response shape in hooks - catch-all test mocks return wrong bodies]]
- [[vi.fn with a zero-arg default locks the mock to a zero-arg signature]]

%% ai-graph-end %%