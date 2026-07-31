---
ai_hash: 8a377893e8628228
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: session 2026-06-17
status: seedling
tags:
- powershell
- downloads
- curl
- gotcha
- mysql
title: dev.mysql.com CDN 403s the PowerShell User-Agent
type: lesson
---

# dev.mysql.com CDN 403s the PowerShell User-Agent

`dev.mysql.com`'s download CDN returns **HTTP 403 with an HTML firewall page** when the request carries the **PowerShell / Invoke-WebRequest User-Agent**, but returns **200** for curl's default UA. (Verified: same URL, IWR UA -> 403 text/html; curl default -> 200 application/zip.)

The nasty part: `Invoke-WebRequest -OutFile` **silently saves the HTML error page** as the target file, so the failure only surfaces later as a confusing `Expand-Archive` 'not a valid zip' error.

**Fix:** download with `curl.exe -fSL` ( `-f` makes HTTP errors non-zero exit instead of saving the body ). In PowerShell, `curl` is an **alias for Invoke-WebRequest**, so you must call `curl.exe` explicitly. Then validate the first two bytes are the ZIP magic `0x50 0x4B` ('PK') before extracting, so a bad download fails loudly.

## Related

- [[Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes]]

%% ai-graph-start %%

**Related notes:**
- [[download.blender.org rejects default urllib User-Agent]]
- [[Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes]]

%% ai-graph-end %%