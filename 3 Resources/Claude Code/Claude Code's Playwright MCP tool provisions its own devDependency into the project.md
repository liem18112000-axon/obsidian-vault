---
title: "Claude Code's Playwright MCP tool provisions its own devDependency into the project"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [claude-code, mcp, playwright, gotcha]
---

# Claude Code's Playwright MCP tool provisions its own devDependency into the project

Claude Code's Playwright MCP server (the browser-automation tool surfaced as `mcp__playwright__*`) provisions a local `playwright` npm package into whatever project directory it's used from — it can add `playwright` to that project's `package.json` `devDependencies` (and update the lockfile to match) as a side effect of its tools being invoked, not because anyone ran `npm install playwright` themselves.

Two things follow from this: (1) don't be alarmed by an unexplained `playwright` devDependency showing up in `git status` after a session that used browser-automation tools — check `.gitignore` for a `.playwright-mcp` (or similar) entry as a tell that this integration is already an established part of the project, and check the `dependencies` vs `devDependencies` placement to confirm it's dev-tooling only, not something that ships in a production/packaged build. (2) The tool also writes its own scratch artifacts (page snapshots, console logs) into a `.playwright-mcp/` folder in the project root — that folder should be gitignored, and a pre-existing gitignore entry for it is a strong signal the integration has been used in that repo before, even if you personally never invoked it there.

Observed in Vinnstack, 2026-07-07: `playwright` appeared in `package.json`/`package-lock.json` mid-session, unrelated to the task at hand (a lib/ folder reorganization); traced to `.playwright-mcp/` already being gitignored with log files dated days earlier, confirming it was pre-existing project infrastructure, not a new or risky change.
