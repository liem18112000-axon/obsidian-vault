---
ai_hash: edd5d8bd3376be88
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: session 2026-07-12 — adding BDD Pipeline as third IntroView tab
status: seedling
tags:
- vinnstack
- nextjs
- ui
- howto
title: How to add a new reference-doc tab to Vinnstack's Introduction overlay
type: howto
---

# How to add a new reference-doc tab to Vinnstack's Introduction overlay

Vinnstack (c:\Users\dvtliem\Kepler\vinnstack) shows static HTML reference docs (doc/*.html) inside the app via an Introduction overlay, opened by clicking the Brand wordmark ("Vinnstack / Agentic Operation System"). To add a new doc as a tab there:

1. Whitelist the doc in app/api/docs/[name]/route.ts's `ALLOWED` map (`{ "url-slug": "filename.html" }`) — this route reads straight off disk by name only, never an arbitrary path, since it is a security-relevant whitelist.
2. Add a matching entry to the `TABS` array in components/ui/IntroView.tsx (`{ id, label, path }`, `path` must match the `ALLOWED` key).

That's the whole wiring — IntroView renders one `<iframe src="/api/docs/{path}?theme={theme}">` per tab and keeps all iframes alive but hidden (`display: none`) so switching tabs is instant and each iframe's own JS state (scroll position, opened modals) survives. No build step is needed since the API route reads the file from disk on every request (`dynamic = "force-dynamic"`), so editing doc/*.html directly reflects on refresh.

The `?theme=` query param + a MutationObserver on `<html>`'s `.dark` class + `postMessage({type:"vinnstack-set-theme"})` keep every open iframe's light/dark mode synced live to the host app's theme, since the app has no global theme-change event to subscribe to directly.

## Related
[[Vinnstack ai-framework.html is aspirational, not the real code]]

%% ai-graph-start %%

**Related notes:**
- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]
- [[Vinnstack ai-framework.html is aspirational, not the real code]]

%% ai-graph-end %%