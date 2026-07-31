---
title: Accesstrade Publisher API authentication
created: 2026-06-11
type: howto
status: seedling
source: research session 2026-06-11
tags:
  - affiliate
  - accesstrade
  - api
  - auth
  - howto
aliases:
  - Accesstrade Token header
  - Accesstrade API key
---

# Accesstrade Publisher API authentication

**Every Accesstrade API request authenticates with a static access key sent in the `Authorization` header using the literal scheme word `Token` (not `Bearer`).** The header is the exact string `Token`, one space, then your key:

```http
Authorization: Token <YOUR_ACCESS_KEY>
Content-Type: application/json
```

This applies to both [[Accesstrade has two API generations|API generations]] and to every HTTP method.

## Where the key comes from

You obtain the access key from the publisher dashboard profile page (classic VN: `pub.accesstrade.vn/accounts/profile`). It is a long-lived key tied to your publisher account — there is no OAuth dance and (per the public docs) no documented short expiry, which makes it convenient but **sensitive**: anyone with it can read your earnings and mint links under your account.

## Minimal call

```bash
curl -H "Authorization: Token $AT_KEY" \
     -H "Content-Type: application/json" \
     "https://api.accesstrade.vn/v1/campaigns?approval=successful"
```

## Critical handling rules

- **Never hardcode the key** in a note, a SKILL.md, a repo, or a canvas. Load it from an environment variable / secret store — see [[Secrets handling for affiliate API keys]].
- The word is **`Token`**, capital T. `Bearer <key>` will be rejected.
- Because it doesn't expire on its own, **rotate it** if it's ever exposed (regenerate in the dashboard).

## Related

- [[Secrets handling for affiliate API keys]]
- [[Accesstrade has two API generations]]
- [[Designing an Accesstrade skill for Claude Code]]
- [[Accesstrade API Integration - MOC]]
