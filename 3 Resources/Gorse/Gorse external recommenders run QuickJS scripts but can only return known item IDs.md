---
title: "Gorse external recommenders run QuickJS scripts but can only return known item IDs"
created: 2026-07-22
type: howto
status: budding
source: "Deep research 2026-07-22 — gorse.io external-recommender docs + gitrec"
tags: [gorse, quickjs, gotcha, config]
---

# Gorse external recommenders run QuickJS scripts but can only return known item IDs

`[[recommend.external]]` (Gorse v0.5+, refined in v0.5.3) embeds a JavaScript script executed by an in-process **QuickJS** engine inside the retrieval pipeline. The script gets synchronous `fetch()` (GET/POST + env-var access) and must return an array of item IDs; those IDs join the candidate pool and are ranked by the FM/LLM ranker like any other source. This is the sanctioned way to inject business lists (offers, editorial picks, external trending) without forking — gitrec injects GitHub Trending this way.

**Gotcha:** "Item IDs that do not exist in the recommender system will be ignored" — business-rule items must already be ingested into Gorse's item catalog before the script can recommend them.

## Related

- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
