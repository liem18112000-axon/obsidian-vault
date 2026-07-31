---
title: "An Electron GUI app can't be smoke-tested from a non-interactive automation session"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [electron, gui, testing, ci, headless, gotcha]
---

# An Electron GUI app can't be smoke-tested from a non-interactive automation session

Finding (Vinnstack exe test, 2026-07-14): tried to launch the packaged Electron exe from the Claude Code Bash tool and probe its local HTTP server. It never served — even with a valid databaseUrl in config.json and a free port. Evidence: no vinnstack.exe process afterward (it exited), yet its cloud-sql-proxy sidecar WAS running (so startup got past ADC + proxy spawn), and nothing ever listened on the pinned port. Two identical attempts.

Conclusion: an Electron GUI app generally can't complete startup when spawned from a non-interactive/detached automation context — it launches, does early headless work, then exits/crashes when it tries to create a BrowserWindow (no usable window station/desktop). A console child (cloud-sql-proxy) runs fine in the same context, which is why the sidecar survived while the app didn't.

Implication: you cannot fully smoke-test a desktop GUI build from a headless tool session. Verify instead by (a) trusting a green CI pipeline that ran the unit tests + production build + packaging, and (b) having a human double-click the exe on a real desktop to confirm the window. If you need an automated UI check, drive it with a proper harness (Playwright/WebDriverIO for Electron) in an environment with a display, not a bare process spawn.

Cleanup note: the crashed app orphaned its cloud-sql-proxy child (Windows doesn't tree-kill on crash), and any config.json edit made for the test must be restored byte-identical afterward.
