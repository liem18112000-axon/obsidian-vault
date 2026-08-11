---
ai_hash: 87c37fa9ff24a92e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities:
- Large Facebook group posts
- article-scan
- profiles
- scraper
- comment-expansion phase
- commenter profiles
- playwright Locator.inner_text
- Timeout 300ms exceeded
- error scanning article link
- group posts
- page posts
- group-post DOM
- inner_text timeout
- article-scan path
- group-post comment DOM
- scan selector
- group-post links
- --max-expand
- group-post comment-article selector
- page-post comment-article selector
- max-expand lever
- empty location is not a broken session
- cost/value link type
- locations
- feed articles
- low profile extraction problem
source: live run 2026-06-14 sample.xlsx
status: seedling
tags:
- fb-info-project
- scraping
- playwright
- gotcha
title: Large FB group posts expand huge but article-scan yields almost no profiles
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]
- [[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[innerText forces layout and can hang Playwright scans on huge DOMs; prefer textContent]]

**Relations:**
- Large Facebook group posts — *exhibit* — huge expansion
- article-scan — *yields almost no* — profiles
- scraper — *spends time in* — comment-expansion phase
- comment-expansion phase — *processes* — feed articles
- scraper — *extracts almost no* — commenter profiles
- article-scan — *throws* — playwright Locator.inner_text: Timeout 300ms exceeded
- playwright Locator.inner_text: Timeout 300ms exceeded — *leads to* — error scanning article link
- error scanning article link — *causes skipping of* — feed articles
- group posts — *are* — worst cost/value link type
- page posts — *yielded* — locations
- low profile extraction problem — *is structural to* — group-post DOM
- Root-cause hypothesis — *suggests* — inner_text timeout is too tight for group-post comment DOM
- Root-cause hypothesis — *suggests* — group-post comment-article structure doesn't match scan selector
- scan selector — *works for* — page posts
- scan selector — *misses* — group posts
- Actionable — *suggests raising* — inner_text timeout
- Actionable — *suggests capping* — --max-expand
- Actionable — *suggests investigating* — group-post comment-article selector
- Actionable — *suggests comparing* — group-post comment-article selector
- group-post comment-article selector — *with* — page-post comment-article selector
- max-expand lever — *is related to* — --max-expand
- empty location is not a broken session — *is related to* — low profile extraction problem

%% ai-graph-end %%