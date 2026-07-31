---
ai_hash: dda4baf1987fdbce
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
source: session 2026-06-09 building zalo-hook-installation skill
status: seedling
tags:
- zalo
- bot-api
- messaging
- claude-code-skills
- reference
title: Zalo Bot API endpoints, token, and message shapes
type: term
---

# Zalo Bot API endpoints, token, and message shapes

The Zalo Bot Platform exposes a Telegram-style bot HTTP API. Create a bot and copy its token at **https://bot.zaloplatforms.com** (token format `numeric_id:secret`, like Telegram); docs live at https://bot.zapps.me/docs.

All calls are `POST` with a JSON body to:

```
https://bot-api.zaloplatforms.com/bot<TOKEN>/<method>
```

**sendMessage** — body `{chat_id, text}`. `text` is 1–2000 chars (Telegram allows 4096, so messages must be truncated more aggressively). Response: `{ok, result:{message_id, date}}` — note `message_id` is a **string**, not an int.

**getUpdates** — body `{timeout}` (long-poll). Its `result` mirrors the webhook payload and may be a **single object OR an array**:

```json
{"ok":true,"result":{
  "event_name":"message.text.received",
  "message":{
    "from":{"id":"...","display_name":"Ted","is_bot":false},
    "chat":{"id":"...","chat_type":"PRIVATE"},
    "text":"Xin chào","message_id":"...","date":1750316131602}}}
```

**First-contact pairing gotcha:** Zalo "pairs" a user to the bot on first contact — the first message a user sends may return a pairing code they must complete before `sendMessage` to that `chat_id` will succeed. So discover `chat_id` by reading `result.message.chat.id` from a `getUpdates` poll after the user messages the bot.

See [[Zalo Bot API has no getUpdates offset and no reply_to_message]] for the design consequences when polling.

## Related

- [[Zalo Bot API has no getUpdates offset and no reply_to_message]]

%% ai-graph-start %%

**Related notes:**
- [[Zalo Bot API has no getUpdates offset and no reply_to_message]]
- [[Zalo OA API is webhook+OAuth and CS messages are rate-limited unlike the Bot API]]
- [[Zalo dev app and bot creation both require a verified Zalo account]]

%% ai-graph-end %%