---
title: "Extract shared use-case code into a sibling shared package, not a peer use case"
created: 2026-06-12
type: lesson
status: seedling
source: "session 2026-06-12, accesstrade_integration use_cases"
tags: [python, architecture, refactoring, packaging, design-decision]
---

# Extract shared use-case code into a sibling shared package, not a peer use case

**When two sibling use cases share data models, extractors, and helpers, factor the shared code into a dedicated `shared/` package rather than letting one use case import from the other.** Cross-importing (use case B reaches into use case A'\''s modules) creates a false dependency — B now breaks if A is refactored or removed, even though they are peers — and it hides where the shared concepts actually live.

Applied in `accesstrade_integration/use_cases`: `campaign_discovery` and `datafeed_brief` both needed the grounded models (FeaturedProduct, Coupon, ContentBrief, ...), the tolerant field extractors, `slugify`, the angle-seed helper, and the `.env` loader. These moved to `use_cases/shared/` (models.py, extract.py, text.py, env.py); both use cases import from `..shared`. A staticmethod that one builder reused from the other (`_suggest_angle`) became a free function `suggest_angle` in `shared/text.py` — reusing a method off another class is a smell that the logic isn'\''t class state and belongs in a shared module.

Signal you'\''ve drawn the boundary right: a peer package'\''s name no longer appears in another peer'\''s imports; only `shared` is imported by multiple peers. Verify with a grep for the old cross-import paths after the move.

Relates to [[Wrap a two-generation API as one shared transport plus one client per generation]] (same instinct: shared core + thin specific layers) and [[Affiliate content-brief generator produces the grounded skeleton, not the prose]].

## Related

- [[Wrap a two-generation API as one shared transport plus one client per generation]]
- [[Affiliate content-brief generator produces the grounded skeleton, not the prose]]
