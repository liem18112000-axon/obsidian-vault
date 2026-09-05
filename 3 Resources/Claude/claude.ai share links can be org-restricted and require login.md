---
title: "claude.ai share links can be org-restricted and require login"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25"
tags: [claude, claude-ai, sharing, gotcha, playwright]
---

# claude.ai share links can be org-restricted and require login

A shared Claude conversation link (`https://claude.ai/share/<uuid>`) is **not always public**. For Team/Enterprise organizations the share is scoped to logged-in org members, so an anonymous fetch fails.

**Symptoms**
- The snapshot API `GET https://claude.ai/api/chat_snapshots/<uuid>?rendering_mode=messages&render_all_tools=true` returns HTTP 403 with body `{"error":{"type":"permission_error","message":"Authentication required"}}`.
- A fresh/headless browser only renders the **"Sign in - Claude"** page (redirects to `/login?returnTo=/share/...`).
- Plain `curl` is separately blocked by Cloudflare ("Just a moment..." interstitial) — it never even reaches the API.

**How to actually download it**
Drive a browser that carries a logged-in claude.ai session:
- Open an interactive login window (Playwright headed) and let the human log in, then capture the `chat_snapshots` JSON response; **or**
- Reuse the user's real Chrome profile via `launchPersistentContext` — but Chrome must be **fully closed** first, or the profile is locked.

**Gotcha:** reading Chrome's cookie / `Local State` files directly to harvest the session is flagged as credential access (and is DPAPI-encrypted). Prefer driving the browser over extracting cookies.

## Related

- [[Excalidraw JSON generator ghost-text: filtering a node's rectangle by id leaves its text elements behind]]
