---
ai_hash: fa1e06f3d622ecfd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-14
status: seedling
tags:
- css
- next-js
- cssnano
- build
- gotcha
- debugging
title: A literal */ inside a CSS comment closes it early and breaks next build's cssnano
  minify
type: lesson
---

# A literal */ inside a CSS comment closes it early and breaks next build's cssnano minify

Gotcha (Vinnstack, 2026-07-14): `next build` failed at CSS minify with `HookWebpackError: Unexpected '/'. Escaping special characters with \ may help.` at a generated static/css line. Root cause: a comment in app/globals.css contained the text "--gherkin-*/--hl-*". CSS comments are non-nesting `/* … */`, so the `*/` sequence INSIDE the prose closed the comment early; the trailing text ("--hl-* … */") was then parsed as CSS, and cssnano choked on a stray `/`. Fix: reword so no `*/` appears inside the comment ("--gherkin-* and --hl-*").

Why it hid: `tsc` never sees CSS. The Next DEV server and even `npx tailwindcss` (no minify) tolerated it — only the production cssnano re-parse failed. So it slips through every check except a real `next build`.

Debugging technique that cracked it: the error names a webpack-virtual css path (not on disk) with a line:col. To SEE the offending CSS, add a temporary `webpack(config,{dev}){ if(!dev) config.optimization.minimize=false; return config }` to next.config.mjs — the build then succeeds and emits the unminified CSS to .next/static/css/*.css, so you can read the exact line. (Filtering optimization.minimizer by name did NOT work — Next's CSS minifier hooks processAssets; `minimize=false` disables it wholesale.) Revert after.

Also: to prove a build failure is pre-existing (not your diff), build the branch's merge-base in a throwaway `git worktree` with a node_modules junction — isolates it from the running dev server. Base failing identically = not your regression.

%% ai-graph-start %%

**Related notes:**
- [[Hydration mismatches only surface as minified errors in production not dev]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Two next dev instances sharing one .next corrupt the webpack PackFileCache]]
- [[Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped in transfer]]

%% ai-graph-end %%