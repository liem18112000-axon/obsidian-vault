---
title: "Offline signed-token licensing for distributed binaries"
created: 2026-06-14
type: concept
status: seedling
source: "fb-info-project session 2026-06-14"
tags: [licensing, cryptography, ed25519, distribution]
---

# Offline signed-token licensing for distributed binaries

Software that runs on the end user's machine with no server (e.g. a PyInstaller exe) can still enforce access control with an **Ed25519-signed token**. The operator keeps the PRIVATE key and signs a tiny payload (`{user, expires, limits}`); the binary embeds only the PUBLIC key and verifies the token **offline**. A user cannot forge a token or extend its expiry without the private key, which never leaves the operator.

**Gotcha that makes or breaks it:** embed the public key as a *compiled constant* in the binary, NOT as an environment variable or config-file value. An env-overridable key lets the user point the binary at their own key and self-sign an unlimited token, defeating the whole scheme.

Token shape that works well: `base64url(payload_json) + "." + base64url(signature)`, one line in a file the user drops next to the app.

See [[Offline licensing: expiry is strong, usage counters only best-effort]] for what this can and cannot guarantee.

## Related

- [[Offline licensing: expiry is strong]]
- [[usage counters only best-effort]]
- [[Vendor pure-Python Ed25519 instead of bundling a crypto wheel]]
