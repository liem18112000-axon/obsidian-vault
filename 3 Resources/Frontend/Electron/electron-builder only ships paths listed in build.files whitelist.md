---
title: "electron-builder only ships paths listed in build.files whitelist"
created: 2026-07-17
type: lesson
status: seedling
source: "Vinnstack session 2026-07-17"
tags: [electron-builder, packaging, vinnstack, gotcha]
---

# electron-builder only ships paths listed in build.files whitelist

electron-builder packages **only** the paths enumerated in `build.files` (package.json). A folder that is not matched by a glob there is simply absent from the installed app under `resources/app`, even though it exists in the repo. There is no warning — the file just is not there at runtime.

In Vinnstack this bit twice: `doc/` (Intro reference HTML) and `vinnstack-skills/` (shipped generic skills) were read at runtime but never listed in `build.files`, so they never shipped. Fix = add `"doc/**/*"` and `"vinnstack-skills/**/*"` to the `files` array.

Rule of thumb: any directory an API route / runtime reads off disk (not just `.next`/`public`) must have an explicit glob in `build.files`, and the code must resolve it relative to the app root — see [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]].

## Related

- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]
