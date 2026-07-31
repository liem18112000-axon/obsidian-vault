---
title: "Two next dev instances sharing one .next corrupt the webpack PackFileCache"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22"
tags: [nextjs, webpack, cache, gotcha]
---

# Two next dev instances sharing one .next corrupt the webpack PackFileCache

Running a second `next dev` (e.g. a throwaway verification server on another port) against the same repo shares the same `.next/` build-cache directory as the primary dev server. The two instances' webpack persistent caches (PackFileCacheStrategy) end up with file-system snapshots that don't match, producing benign-but-noisy warnings on the next compile:

`[webpack.cache.PackFileCacheStrategy/webpack.FileSystemInfo] Resolving '…/.next/server/app/api/…/route' … doesn't lead to expected result … Resolving dependencies are ignored for this path.`

Key facts:
- **Benign** — `<w>` level; only build-cache dependency tracking is skipped. Dev serving and routes work normally.
- The referenced `.next/server/...` path is a *production* layout that doesn't exist in dev, which is why resolution fails.
- **Fix:** stop the dev server, `Remove-Item -Recurse -Force .next`, restart. Never delete `.next` under a running server.
- **Prevention:** for throwaway verification servers, point Next at a separate build dir (`next dev` doesn't take a distDir flag — set `distDir` via env-driven config, or simply accept the warning / clean up after), or prefer hitting the user's already-running instance.
