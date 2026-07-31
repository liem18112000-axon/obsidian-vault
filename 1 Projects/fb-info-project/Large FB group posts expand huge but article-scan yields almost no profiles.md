---
title: "Large FB group posts expand huge but article-scan yields almost no profiles"
created: 2026-06-14
type: lesson
status: seedling
source: "live run 2026-06-14 sample.xlsx"
tags: [fb-info-project, scraping, playwright, gotcha]
---

# Large FB group posts expand huge but article-scan yields almost no profiles

On large Facebook **group** posts, the scraper can burn ~60 minutes in the comment-expansion phase (≈200 expand batches, ≈710 feed articles) and still extract almost **no commenter profiles** — observed **1 profile out of ~1498 candidate links**. The article-scan step throws repeated `playwright Locator.inner_text: Timeout 300ms exceeded` → `error scanning article link`, so nearly every comment article is skipped.

**Why it matters:** group posts are the worst cost/value link type. In the same 2026-06-14 live run on `resources/sample.xlsx`, ordinary *page* posts (links 4 and 6) yielded 16–17 locations fine with the identical session — so this is structural to group-post DOM, not an auth problem.

**Root-cause hypothesis:** the 300ms `inner_text` timeout in the article-scan path is too tight for the group-post comment DOM, or the group-post comment-article structure doesn't match the scan selector (works for page posts, misses group posts).

**Actionable:**
- For group-post links, raise the article-scan `inner_text` timeout, and/or cap `--max-expand` low so you don't pay ~60 min to expand a feed that scan can't read anyway.
- Investigate the group-post comment-article selector vs the page-post one.

Related: [[max-expand lever]], [[empty location is not a broken session]].

## Related

- [[max-expand lever]]
- [[empty location is not a broken session]]
