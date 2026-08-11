---
ai_hash: 9a5e8082ff5dbfe4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: code-review session 2026-06-10, fb-info-project
status: seedling
tags:
- python
- gotcha
- json
- normalization
title: JSON null survives str() as 'None' and corrupts lookup-table normalization
type: lesson
---

# JSON null survives str() as 'None' and corrupts lookup-table normalization

A JSON `null` field read in Python becomes `None`, and `str(None)` is the string `'None'` — so a case-insensitive lookup like `TABLE.get(str(value).lower())` silently hits a real `'none'` key instead of falling through to the default. In cookie imports this turned `"sameSite": null` (meaning *unspecified*) into SameSite `None` (meaning *no restriction*) — the opposite of the safe default.

Guard the coercion: `str(value or '').lower()` (or check `is None` explicitly) so null routes to the same default as a missing key.

Found while reviewing a cookie-normalization function that accepted browser-extension JSON exports.

## Related

- [[SameSite=None cookies require Secure or Chromium drops them]]

%% ai-graph-start %%

**Related notes:**
- [[SameSite=None cookies require Secure or Chromium drops them]]

%% ai-graph-end %%