---
ai_hash: 5643e06376fca694
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-21
entities: []
source: session 2026-06-21
status: seedling
tags:
- facebook
- scraping
- uid
- gotcha
- fb-info-project
title: Facebook UID from a vanity handle via the mbasic lst token
type: howto
---

# Facebook UID from a vanity handle via the mbasic lst token

On the lightweight **mbasic.facebook.com** rendering of a profile, the owner's numeric UID is embedded in the `lst` token that appears in nearly every action link:

```
lst=<viewerId>:<ownerId>:<timestamp>
```

The **middle** value is the profile owner's UID. Colons are frequently URL-encoded as `%3A`, so a tolerant regex is needed: `lst=\d+(?::|%3A)(\d+)(?::|%3A)`.

**Why it matters:** the full `www.facebook.com` page only exposes a vanity profile's UID buried inside JSON blobs (`userID`, `entity_id`, `identifier`, the `al:android:url` = `fb://profile/<id>` meta) that are sometimes absent or obfuscated. mbasic is server-rendered minimal HTML that leaks the owner id far more readily, and it needs no third-party "find Facebook id" service (those are Cloudflare-gated and browser-only).

**When it applies:** use it as a *fallback* — parse the full page first; only if that yields no UID, refetch `mbasic.facebook.com/<handle>` (reusing the same authenticated session) and read the `lst` token. A `profile.php?id=<id>` link needs none of this — the id is already in the URL.

In `fb-info-project` this is `MBASIC_UID_RX` in `patterns.py`, with `extract_uid_mbasic()` falling back to the generic `UID_RX` when no `lst` token is present.

%% ai-graph-start %%

**Related notes:**
- [[Facebook page userID is the viewer not the profile owner]]
- [[Facebook Comet comment DOM does not expose the commenter's numeric UID]]

%% ai-graph-end %%