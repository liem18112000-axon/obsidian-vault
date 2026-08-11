---
ai_hash: 4145b3ee769bb133
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-29
entities: []
source: session 2026-06-29
status: seedling
tags:
- claude-code
- ccusage
- token-usage
- tooling
title: ccusage — CLI tool for Claude Code token usage inspection
type: howto
---

# ccusage — CLI tool for Claude Code token usage inspection

ccusage is a CLI tool that reads your local Claude Code transcript files and reports token counts and cost-equivalent figures per session, day, and model.

## How to use

```
ccusage              # all-time summary by day/model
ccusage blocks --live  # remaining capacity in the current 5-hour rate-limit window
```

`ccusage blocks --live` is the closest proxy to "how much of my rate limit is left" — Claude Code enforces a rolling 5-hour token budget, and this command shows where you are inside that window.

## Reading the output

- **Input / output / cache-read tokens** — shown separately; cache-read tokens are much cheaper.
- **Cost** — displayed as an API-rate equivalent (what it would cost at pay-per-token rates), not what you are actually charged on a Max/Pro subscription.

## Related

- [[Claude Code Max/Pro subscription reports API-rate equivalent cost]]
- [[not actual billing]]
- [[High cache-read ratio in Claude Code signals healthy prompt caching]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Code hooks see no token usage in their payload; read the transcript usage entries instead]]

%% ai-graph-end %%