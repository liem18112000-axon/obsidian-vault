---
ai_hash: 24b806b97ee68559
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities: []
tags:
- nextjs
- react
- hydration
- ssr
- production
- debugging
---

# Hydration mismatches only surface as minified errors in production not dev

A Next.js App-Router page that renders **non-deterministic content during render** — `Math.random()`, `Date.now()` / `new Date()` timestamps, locale/timezone-dependent formatting — produces different HTML on the server than on the client. In `next dev` this shows up as a readable, often-tolerated hydration warning; in a **production build** (`next start` / the standalone Docker image) the same mismatch throws minified React errors:

- **#418** — Hydration failed; initial UI doesn't match the server.
- **#423** — error while hydrating.
- **#425** — text content does not match server-rendered HTML.

The page still *works* — React discards the SSR output and re-renders on the client — so you get HTTP 200 and a correct-looking UI while the console fills with these errors. That's why running the production/Docker build catches bugs `next dev` hides.

**Fixes:** compute random/time values in a `useEffect` (after mount) instead of during render; or gate the varying subtree behind a mounted flag; or `suppressHydrationWarning` for genuinely unavoidable cases (e.g. a clock). Module-level `Math.random()` seeding data is a common culprit because the server and client bundles evaluate the module separately, so the "constants" differ.

Related: [[Next.js standalone Docker image must copy public and .next static next to server.js]].

%% ai-graph-start %%

**Related notes:**
- [[Two next dev instances sharing one .next corrupt the webpack PackFileCache]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Next.js standalone Docker image must copy public and .next static next to server.js]]
- [[A literal inside a CSS comment closes it early and breaks next build's cssnano minify]]
- [[Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped in transfer]]

%% ai-graph-end %%