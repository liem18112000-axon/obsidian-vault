---
ai_hash: 3547ba286754966e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-14, Vinnstack exe test
status: seedling
tags:
- electron
- gui
- testing
- ci
- headless
- gotcha
title: An Electron GUI app can't be smoke-tested from a non-interactive automation
  session
type: lesson
---

# An Electron GUI app can't be smoke-tested from a non-interactive automation session

Launching a packaged Electron exe from an agent/CI shell and probing its local HTTP server does not work as a smoke test. Observed (Vinnstack, 2026-07-14, twice): the exe exited with nothing listening on the pinned port, while its console-child sidecar (cloud-sql-proxy) kept running — so startup got past ADC + proxy spawn but produced no window and no service.

> [!warning] The original root-cause guess here ("no usable window station/desktop, so BrowserWindow creation crashes") was **wrong** for this incident — the real cause was `ELECTRON_RUN_AS_NODE=1` leaking from the tool session. Check that first: [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]].

The practical rule still holds: don't treat a bare process spawn from a headless tool session as proof a desktop build works. Verify with (a) a green CI run of unit tests + production build + packaging, (b) a human double-clicking the exe on a real desktop, or (c) a real Electron UI harness (Playwright / WebDriverIO for Electron) in an environment with a display.

Cleanup: a crashed Electron app orphans its child processes (Windows doesn't tree-kill), and any `config.json` edited for the test must be restored byte-identical.

%% ai-graph-start %%

**Related notes:**
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]]
- [[Open-and-degrade beats hard-quit let a desktop app start without its optional DB (Vinnstack clean-Win fix)]]
- [[Electron GPU process launch failure is fatal; disable hardware acceleration to avoid it]]
- [[Cross-building Electron Windows exe on Linux needs wine]]

%% ai-graph-end %%