---
title: "Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes"
created: 2026-06-17
type: lesson
status: seedling
source: "session 2026-06-17"
tags: [powershell, encoding, gotcha]
---

# Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes

**Windows PowerShell 5.1** reads a script file with **no BOM as ANSI (CP1252)**, not UTF-8 (pwsh 7+ defaults to UTF-8 and is unaffected).

So a UTF-8 em-dash `—` (bytes E2 80 94) is misread; byte `0x94` in CP1252 is U+201D, a **'smart' closing double-quote that PowerShell treats as a string delimiter**. The result is a bogus parser error like `Missing closing '}'` reported **far from the real location** (wherever the quote imbalance finally bites).

**Fix:** keep `.ps1` build/CI scripts **ASCII-only**, or save them with a **UTF-8 BOM**. Detect by parsing the file read as ANSI: `[Parser]::ParseInput([IO.File]::ReadAllText($p,[Text.Encoding]::Default),...)` reproduces the error while a UTF-8 read shows 0 errors.

## Related

- [[dev.mysql.com CDN 403s the PowerShell User-Agent]]
