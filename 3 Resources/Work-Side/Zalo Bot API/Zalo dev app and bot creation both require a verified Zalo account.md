---
title: "Zalo dev app and bot creation both require a verified Zalo account"
created: 2026-06-09
type: lesson
status: seedling
source: "session 2026-06-09 — user hit the gate on both bot and app creation"
tags: [zalo, account-verification, gotcha, blocker, kyc]
---

# Zalo dev app and bot creation both require a verified Zalo account

Both Zalo Bot Platform (bot.zaloplatforms.com) bot creation AND Zalo developer-app creation (developers.zalo.me) are gated behind **Zalo account verification** ("xác thực tài khoản"). An unverified account hits, on app creation:

> "Tài khoản Zalo chưa được xác thực. Vui lòng xác thực tài khoản Zalo trước khi tạo ứng dụng."

Key lesson: when a Zalo account "cannot create a bot", the cause is almost certainly this account-level gate, NOT the Bot-vs-OA API choice — so switching from the Bot API to the OA API does NOT help, because OA needs a developer app, which needs the same verification. Diagnose the account gate FIRST before building either integration.

Verification ("xác thực"/"định danh tài khoản") is done in the Zalo mobile app: Cài đặt → Tài khoản và bảo mật → Định danh tài khoản, typically requiring a phone number and (for Vietnam) national ID (CCCD) eKYC. Until that succeeds, no Claude↔Zalo bridge (bot or OA) is possible — Telegram/Slack are the working fallbacks.

Relates to [[Zalo OA API is webhook+OAuth and CS messages are rate-limited unlike the Bot API]] and [[Zalo Bot API endpoints, token, and message shapes]].

## Related

- [[Zalo OA API is webhook+OAuth and CS messages are rate-limited unlike the Bot API]]
- [[3 Resources/Work-Side/Zalo Bot API/Zalo Bot API endpoints, token, and message shapes]]
