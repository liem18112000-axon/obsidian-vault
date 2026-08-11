---
ai_hash: ce176ecd99d1bebf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: session 2026-07-22
status: seedling
tags:
- nextjs
- webpack
- cache
- gotcha
title: Two next dev instances sharing one .next corrupt the webpack PackFileCache
type: lesson
---

# Two next dev instances sharing one .next corrupt the webpack PackFileCache

Running a second `next dev` (e.g. a throwaway verification server on another port) against the same repo shares the same `.next/` build-cache directory as the primary dev server. The two instances' webpack persistent caches (PackFileCacheStrategy) end up with file-system snapshots that don't match, producing benign-but-noisy warnings on the next compile:

`[webpack.cache.PackFileCacheStrategy/webpack.FileSystemInfo] Resolving '…/.next/server/app/api/…/route' … doesn't lead to expected result … Resolving dependencies are ignored for this path.`

Key facts:
- **Benign** — `<w>` level; only build-cache dependency tracking is skipped. Dev serving and routes work normally.
- The referenced `.next/server/...` path is a *production* layout that doesn't exist in dev, which is why resolution fails.
- **Fix:** stop the dev server, `Remove-Item -Recurse -Force .next`, restart. Never delete `.next` under a running server.
- **Prevention:** for throwaway verification servers, point Next at a separate build dir (`next dev` doesn't take a distDir flag — set `distDir` via env-driven config, or simply accept the warning / clean up after), or prefer hitting the user's already-running instance.

%% ai-graph-start %%

**Related notes:**
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Next.js .nextcache is a build-time-only cache, never bundle it]]
- [[Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped in transfer]]
- [[Hydration mismatches only surface as minified errors in production not dev]]
- [[NextAuth cannot share apiauth with an existing dynamic route - single segments get shadowed]]

%% ai-graph-end %%