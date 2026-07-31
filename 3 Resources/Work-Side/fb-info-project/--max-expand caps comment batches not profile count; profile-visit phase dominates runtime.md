---
ai_hash: 17f67f2574e60843
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: live run 2026-06-14
status: seedling
tags:
- fb-info-project
- performance
- facebook
- scraping
title: --max-expand caps comment batches not profile count; profile-visit phase dominates
  runtime
type: observation
---

# --max-expand caps comment batches not profile count; profile-visit phase dominates runtime

In fb-info-project, the `--max-expand` flag caps how many 'view more comments' batches are clicked — **not** how many commenter profiles are then visited. A single photo/post with an active thread yielded **209 commenters from just 20 comment batches** (the cap), so lowering `--max-expand` barely shortened the run.

The real runtime cost is the **profile-visit phase**: each of the N collected profiles is fetched and parsed, and on a live run many `Page.goto` calls hit `Timeout 30000ms exceeded` and retry 3× (~90s wasted per failing profile) — Facebook throttling rapid sequential profile loads. So total wall-clock scales with **commenter count**, dominated by these timeouts, not with the expand cap.

Practical levers for a faster run: choose input with few commenters; (not yet available) a flag to cap the number of profiles visited; or raise concurrency / lower the per-page goto timeout. `MAX_PAGES=5` (config.py) already runs 5 profile tabs concurrently.

Note: the fresh-session run DID extract real locations (Hải Phòng, Ho Chi Minh City, Hanoi, Biên Hòa, Montreal, ...), so timeouts are a throughput problem, not an auth problem — contrast with the stale-session signature.

## Related

- [[fb-scraper writes output per link only at the end; killing mid-run loses the whole link]]

%% ai-graph-start %%

**Related notes:**
- [[fb-scraper writes output per link only at the end; killing mid-run loses the whole link]]
- [[scrapling goto waits for load event + retries=3; on FB SPA that means ~90s per dead profile]]
- [[Large FB group posts expand huge but article-scan yields almost no profiles]]
- [[Distinguish absent control from missed click when expanding lazy lists]]
- [[Switch Facebook comment sort to All comments before any scrolling or expansion]]

%% ai-graph-end %%