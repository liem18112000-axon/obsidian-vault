---
ai_hash: 6900c015309d1c8b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
source: session 2026-06-09 adapting zalo skill after bot-creation was blocked
status: seedling
tags:
- zalo
- oa-api
- oauth
- webhook
- gotcha
- messaging
- claude-code-skills
title: Zalo OA API is webhook+OAuth and CS messages are rate-limited unlike the Bot
  API
type: lesson
---

# Zalo OA API is webhook+OAuth and CS messages are rate-limited unlike the Bot API

The Zalo **Official Account (OA) API** (`openapi.zalo.me/v3.0/oa/...`, docs at developers.zalo.me) is a different beast from the newer Zalo **Bot** API ([[Zalo Bot API endpoints, token, and message shapes]]) and is a poor fit for a "notify me whenever Claude finishes" personal bridge. Three structural reasons:

**1. OAuth + rotating refresh token, not a static token.** You register an app (app_id, app_secret) on developers.zalo.me, link an OA, and do a browser consent at `https://oauth.zaloapp.com/v4/oa/permission?app_id=...&redirect_uri=...` to get a `code`. Exchange it at `POST https://oauth.zaloapp.com/v4/oa/access_token` (header `secret_key: <app_secret>`, form body `code, app_id, grant_type=authorization_code`) for `{access_token, refresh_token, expires_in}`. The access_token is short-lived (~25h) and **refresh rotates the refresh_token** — you must persist the newest refresh_token to disk or you get locked out. A background refresher is mandatory.

**2. Receiving is webhook-only — no polling.** Unlike the Bot APIs `getUpdates` long-poll, the OA pushes incoming user messages to a webhook URL you configure in OA settings. Events like `user_send_text` arrive as `{sender:{id:<user_id>}, recipient:{id:<oa_id>}, event_name, message:{text, msg_id}, timestamp}` with an HMAC `mac` to verify. So a reverse channel needs a public HTTPS endpoint (e.g. an ngrok tunnel) + an HTTP server, not a simple poller daemon.

**3. CS messages are rate/window-limited — the dealbreaker for arbitrary pings.** Send text via `POST https://openapi.zalo.me/v3.0/oa/message/cs` (header `access_token`, body `{recipient:{user_id}, message:{text}}`). But "consultation" (CS) messages can only go to a user who interacted recently AND are rate-limited per user — error `-214: OA đã gửi tin nhắn cho người dùng trong 24h gần đây` ("OA already messaged this user in the last 24h"). The OA model is built for customer-service *replies within a window*, not for a bot that pings you on every Stop event. Frequent Claude→you notifications will hit the limit fast.

**Takeaway:** if someones Zalo account cant create a Bot (the account gate), the OA route is heavier (OAuth+webhook+tunnel) AND its forward channel is throttled exactly where the use case needs it. Telegram/Slack are better fallbacks for the always-on notify use case.

## Related

- [[3 Resources/Work-Side/Zalo Bot API/Zalo Bot API endpoints, token, and message shapes]]
- [[Zalo Bot API has no getUpdates offset and no reply_to_message]]

%% ai-graph-start %%

**Related notes:**
- [[Zalo Bot API endpoints, token, and message shapes]]
- [[Zalo dev app and bot creation both require a verified Zalo account]]
- [[Zalo Bot API has no getUpdates offset and no reply_to_message]]

%% ai-graph-end %%