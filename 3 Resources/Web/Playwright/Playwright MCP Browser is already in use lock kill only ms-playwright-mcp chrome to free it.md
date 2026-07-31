---
title: "Playwright MCP Browser is already in use lock kill only ms-playwright-mcp chrome to free it"
created: 2026-07-25
type: lesson
status: seedling
source: "session 2026-07-25"
tags: [playwright, mcp, windows, gotcha, chrome]
---

# Playwright MCP Browser is already in use lock kill only ms-playwright-mcp chrome to free it

After the Playwright MCP browser idle-closes, the next `browser_navigate`/`browser_close` can fail with `Error: Browser is already in use for C:\Users\...\ms-playwright-mcp\mcp-chrome-<id>, use --isolated to run multiple instances` — a stale chrome process is still holding the profile lock.

Fix: kill ONLY the MCP-launched chrome, identified by `ms-playwright-mcp` in its command line, then navigate again. In PowerShell:

```powershell
Get-CimInstance Win32_Process -Filter "Name=chrome.exe" |
  Where-Object { $_.CommandLine -like *ms-playwright-mcp* } |
  ForEach-Object { Stop-Process -Id $_.ProcessId -Force }
```

Do NOT blanket-kill chrome.exe — that would close the users personal browser. The MCP profile persists on disk, so its **session cookies survive** the restart; a fresh `browser_navigate` reuses the existing login (no re-auth needed).

Related: [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]].

## Related

- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]
