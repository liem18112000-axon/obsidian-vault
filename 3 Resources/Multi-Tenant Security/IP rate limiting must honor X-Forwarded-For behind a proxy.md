---
title: "IP rate limiting must honor X-Forwarded-For behind a proxy"
created: 2026-09-05
type: lesson
status: seedling
source: "c360 python code review 2026-09-05"
tags: [rate-limiting, proxy, security, gotcha]
---

# IP rate limiting must honor X-Forwarded-For behind a proxy

Rate limiters that key on `request.client.host` (the immediate socket peer) are miscoped behind a reverse proxy / load balancer, where that address is the proxy IP for every request. All clients then share one counter: one abuser trips the limit for everyone (DoS), and real per-client throttling never engages.

Behind a trusted proxy, derive the client IP from a validated `X-Forwarded-For` (leftmost untrusted hop) or the platform's real-IP header, and scope brute-force limits per account/username too, not IP alone.
