---
title: "Packaged Electron+Next.js API routes must not use process.cwd() for bundled files"
created: 2026-07-17
type: lesson
status: seedling
source: "Vinnstack session 2026-07-17"
tags: [electron, nextjs, vinnstack, gotcha, packaging]
---

# Packaged Electron+Next.js API routes must not use process.cwd() for bundled files

In a packaged Electron app that hosts an in-process Next.js server, an API route must NOT resolve bundled files via `process.cwd()`. In the installed build, cwd is the **exe launch directory** (e.g. `...\Programs\vinnstack\`), not `resources/app` where the files live — so `path.join(process.cwd(), "doc", file)` silently 404s. It works in `next dev` only because the dev server runs with cwd = project root.

**Fix (Vinnstack):** `electron/main.js` sets `process.env.VINNSTACK_APP_DIR = projectDir` (= `resources/app`) *before* `nextApp.prepare()`, and a shared helper `lib/shared/appRoot.ts` resolves `process.env.VINNSTACK_APP_DIR || process.cwd()` (the `|| cwd` keeps dev working). All on-disk-asset routes call `appPath("doc", file)`.

**Symptom that surfaced it:** clicking the Vinnstack logo opened IntroView, whose 3 iframe tabs hit `/api/docs/[name]` and all showed "doc not found on disk" — but only in the installed app, never in dev.

Pairs with the packaging gotcha: the files must also be *included* in the build. See [[electron-builder only ships paths listed in build.files whitelist]].

## Related

- [[electron-builder only ships paths listed in build.files whitelist]]
