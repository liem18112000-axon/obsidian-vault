---
ai_hash: 5d39ff8b2d07f24c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07
status: seedling
tags:
- claude-code
- mcp
- playwright
- gotcha
title: Claude Code's Playwright MCP tool provisions its own devDependency into the
  project
type: lesson
---

# Claude Code's Playwright MCP tool provisions its own devDependency into the project

Claude Code's Playwright MCP server (the browser-automation tool surfaced as `mcp__playwright__*`) provisions a local `playwright` npm package into whatever project directory it's used from — it can add `playwright` to that project's `package.json` `devDependencies` (and update the lockfile to match) as a side effect of its tools being invoked, not because anyone ran `npm install playwright` themselves.

It also writes scratch artifacts (page snapshots, console logs) into a `.playwright-mcp/` folder in the project root.

So when an unexplained `playwright` devDependency shows up in `git status`: check `.gitignore` for a `.playwright-mcp` entry — a pre-existing one proves the integration is established project infrastructure, not something new you introduced — and confirm the package landed in `devDependencies`, not `dependencies` (it must not ship in a packaged build). Gitignore `.playwright-mcp/` if it isn't already.

Observed in Vinnstack, 2026-07-07: `playwright` appeared in `package.json`/`package-lock.json` mid-session during an unrelated `lib/` reorganization; `.playwright-mcp/` was already gitignored with log files dated days earlier.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%