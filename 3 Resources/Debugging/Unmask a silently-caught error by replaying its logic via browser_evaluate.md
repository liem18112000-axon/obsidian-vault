---
title: "Unmask a silently-caught error by replaying its logic via browser_evaluate"
created: 2026-07-08
type: howto
source: "vinnstack session 2026-07-08"
tags: [debugging, playwright, browser]
---

# Unmask a silently-caught error by replaying its logic via browser_evaluate

When a bug is hidden behind a silent `catch { /* ... */ }` block (no logging, no rethrow), don't guess at the cause from reading the code alone — reproduce it live and replay the exact same logic inline in the running page to force the real error to surface.

Concretely (using a Playwright-driven browser as the tool): extract the actual live DOM/data state the buggy code would have used (e.g. `document.querySelector(...).outerHTML`), then re-implement the function's steps one by one in a `browser_evaluate` call — same parsing, same API calls (canvas, clipboard, fetch, etc.) — but replace the swallowing `catch` with one that returns the caught error's message. This gets you the exact thrown error (e.g. a specific `DOMException` message) without needing to add temporary logging to the source and redeploy/rebuild.

This is especially valuable for browser-API failures (clipboard, canvas, storage permissions) that only manifest at runtime and differ by environment, where static code reading can't reveal the actual failure mode.
