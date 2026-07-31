---
ai_hash: 5c34776430dd9f71
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-17
entities: []
source: Vinnstack session 2026-07-17
status: seedling
tags:
- electron-builder
- packaging
- vinnstack
- gotcha
title: electron-builder only ships paths listed in build.files whitelist
type: lesson
---

# electron-builder only ships paths listed in build.files whitelist

electron-builder packages **only** the paths enumerated in `build.files` (package.json). A folder that is not matched by a glob there is simply absent from the installed app under `resources/app`, even though it exists in the repo. There is no warning — the file just is not there at runtime.

In Vinnstack this bit twice: `doc/` (Intro reference HTML) and `vinnstack-skills/` (shipped generic skills) were read at runtime but never listed in `build.files`, so they never shipped. Fix = add `"doc/**/*"` and `"vinnstack-skills/**/*"` to the `files` array.

Rule of thumb: any directory an API route / runtime reads off disk (not just `.next`/`public`) must have an explicit glob in `build.files`, and the code must resolve it relative to the app root — see [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]].

## Related

- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]

%% ai-graph-start %%

**Related notes:**
- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]
- [[electron-builder files node_modules glob disables devDependency pruning]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
- [[electron-builder --win CLI flag overrides win.target in package.json]]
- [[Dead bundling config outlives the runtime code that read it]]

%% ai-graph-end %%