---
title: "Next.js production server never loads the native SWC binary at runtime"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack build optimization session"
tags: [nextjs, swc, cross-platform, ci-cd]
---

# Next.js production server never loads the native SWC binary at runtime

Next.js's native SWC compiler binary (`@next/swc-<platform>`, a `.node` addon) is only ever `require()`'d from build-time and dev-mode code paths — `next/dist/build/swc/index.js` (used by `next build` and Turbopack/webpack dev hot-reload) and one experimental `useLightningcss` config check. Nothing under `next/dist/server/` (the request-serving code that `nextApp.prepare()` + `getRequestHandler()` run in a production/`dev:false` server) references `@next/swc` or `loadBindings` at all — confirmed by grepping Next's own installed source, not assuming from docs.

Practical consequence: a Next.js production server never needs the native SWC binary for the platform it's running on — only the machine that ran `next build` needs the SWC binary matching *that* machine's platform. This matters for CI pipelines that cross-build for another OS/arch (e.g. building a Windows-targeted Electron app on a Linux CI runner): `npm ci` on Linux only fetches the Linux `@next/swc` variant (npm's optional-dependency platform matching skips non-matching platforms), never the Windows one — and that's fine, because the compiled `.next/server` + `.next/static` output the build produces doesn't need SWC again once it exists; only re-running `next build` would.

Related: [[electron-builder asar requires asarUnpack for native .node addons]] — the native-addon-unpacking concern this note resolves for a specific case (a CI-built package literally won't contain the platform-mismatched swc folder at all, so unpacking it is a moot no-op there; it only matters for a build done natively on the target platform).

## Related

- [[electron-builder asar requires asarUnpack for native .node addons]]
