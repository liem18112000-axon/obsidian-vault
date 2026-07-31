---
title: "Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions"
created: 2026-07-22
type: howto
status: budding
source: "Deep research 2026-07-22 — gorse.io v0.5 release post + gitrec config.toml"
tags: [gorse, config, expr, recommender-systems]
---

# Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions

In Gorse v0.5 you build custom recommendation behavior by declaring multiple **named instances** of config blocks (this replaced v0.4's single similar-item/similar-user recommenders):

- `[[recommend.non-personalized]]` — leaderboards written in the **Expr** language, e.g. a weekly Trending board:
  `score = "count(feedback, .FeedbackType == 'purchase')"` with `filter = "(now() - item.Timestamp).Hours() < 168"`.
- `[[recommend.item-to-item]]` / `[[recommend.user-to-user]]` — similarity recommenders with `type = tags | users | embedding` (master additionally validates undocumented `chat` and `auto`); the expr `column` picks the item field, e.g. `column = "item.Labels.embedding"` computes Euclidean neighbors over **externally supplied embeddings** — the sanctioned way to bring your own representation model into Gorse.

Named instances are then referenced as `non-personalized/trending_weekly`, `item-to-item/neighbors`, etc. in `[recommend.ranker].recommenders` (the merge list) and `[recommend.fallback]`.

## Related

- [[Gorse external recommenders run QuickJS scripts but can only return known item IDs]]
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
