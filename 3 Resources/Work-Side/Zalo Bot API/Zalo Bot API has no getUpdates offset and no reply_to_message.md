---
ai_hash: 524f0f22e2ca5049
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
source: session 2026-06-09 building zalo-hook-installation skill
status: seedling
tags:
- zalo
- bot-api
- gotcha
- messaging
- claude-code-skills
title: Zalo Bot API has no getUpdates offset and no reply_to_message
type: lesson
---

# Zalo Bot API has no getUpdates offset and no reply_to_message

The Zalo Bot API is Telegram-shaped but is missing two fields that Telegram bridges rely on, and each forces a design change when building a long-poll → resume integration:

**1. `getUpdates` has no `offset`/`update_id`.** You cannot acknowledge consumed updates by advancing an offset like Telegram. The server long-poll may redeliver. Mitigation: the client must dedupe itself — track processed `message_id`s in a bounded persisted set, and ignore any message whose `date` (epoch ms) is older than the poller start time so a fresh poller does not replay history.

**2. Incoming messages have no `reply_to_message`.** A user reply carries no reference to the bot message it answers, so you cannot map a reply back to a specific originating session the way the Telegram bridge does (message_id → session_id map). Mitigation: route every incoming message to the **most-recent** session instead — the forward hook overwrites a single `last-session.json` pointer on each Stop/Notification, and the poller resumes that. Provide an explicit escape hatch (e.g. a `/new <task>` command) to force a fresh session.

Discovered while building the `zalo-hook-installation` Claude Code skill (the Zalo sibling of [[telegram-hook-installation]]-style bridges). Contrast with Telegram, which has both `offset` and `reply_to_message`.

API surface details in [[Zalo Bot API endpoints, token, and message shapes]].

## Related

- [[3 Resources/Work-Side/Zalo Bot API/Zalo Bot API endpoints, token, and message shapes]]

%% ai-graph-start %%

**Related notes:**
- [[Zalo Bot API endpoints, token, and message shapes]]
- [[Zalo OA API is webhook+OAuth and CS messages are rate-limited unlike the Bot API]]
- [[Zalo dev app and bot creation both require a verified Zalo account]]

%% ai-graph-end %%