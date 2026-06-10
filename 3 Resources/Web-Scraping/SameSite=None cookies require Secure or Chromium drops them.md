---
title: "SameSite=None cookies require Secure or Chromium drops them"
created: 2026-06-10
type: lesson
status: seedling
source: "code-review session 2026-06-10, fb-info-project"
tags: [cookies, playwright, chromium, gotcha]
---

# SameSite=None cookies require Secure or Chromium drops them

Per RFC 6265bis, a cookie with `SameSite=None` (sent on all cross-site requests) is only valid when it also carries `Secure`. Chromium rejects or silently drops a `SameSite=None` cookie without `Secure` — and in Playwright one bad cookie can fail the whole `add_cookies()` call, so an imported session dies on a single malformed entry.

Practical rule for anything that imports/normalizes cookies (scrapers, session restorers): whenever the output sameSite is `None`, force `secure: true`. For HTTPS-only sites like facebook.com this is always safe.

See [[JSON null survives str() as 'None' and corrupts lookup-table normalization]] for how a null sameSite accidentally *produces* this invalid combination.

## Related

- [[JSON null survives str() as 'None' and corrupts lookup-table normalization]]
