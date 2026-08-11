---
ai_hash: 7f23fe1e63d833ad
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: sessions 2026-06-17 and 2026-07-07 (vinnstack scripts/build-exe.ps1)
status: seedling
tags:
- powershell
- windows
- encoding
- gotcha
title: Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes
type: lesson
---

# Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes

**Windows PowerShell 5.1** (`powershell.exe`) reads a `.ps1` with **no BOM as ANSI (CP1252)**, not UTF-8 — pwsh 7+ defaults to UTF-8 and is unaffected. A UTF-8 em-dash `—` (bytes `E2 80 94`) is therefore misdecoded, and `0x94` in CP1252 is U+201D, a **smart closing double-quote that PowerShell treats as a string delimiter**. Any non-ASCII punctuation inside an executable string literal can thus terminate the string early.

**Symptom:** bogus parse errors reported far from the real location — `The string is missing the terminator` / `Missing closing '}' in statement block` — pointing at (or near) a line with an em dash inside a string like `Fail "...—..."`.

**Comments are safe:** `<# ... #>` blocks are scanned only for the closing `#>`, not for balanced quotes, so em dashes there never break the parse. That is why other scripts in the same repo with em dashes in comments never hit this.

**Fix:** keep `.ps1` files ASCII-only inside executable code (use `-` / `--`; reserve em dashes for comments and docs), or save the file with a **UTF-8 BOM**. Files written by UTF-8-writing tools are BOM-less by default, which is how this gets introduced.

**Detect:** parse the file read as ANSI — `[Parser]::ParseInput([IO.File]::ReadAllText($p,[Text.Encoding]::Default),...)` reproduces the error, while a UTF-8 read shows 0 errors.

## Related

- [[dev.mysql.com CDN 403s the PowerShell User-Agent]]
- [[PowerShell scripting conventions]]

%% ai-graph-start %%

**Related notes:**
- [[PowerShell here-string @'...'@ silently corrupts git commit messages in the Bash tool]]
- [[Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON]]
- [[Windows cp1252 console crashes on non-ASCII Python prints; force UTF-8]]
- [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]]
- [[dev.mysql.com CDN 403s the PowerShell User-Agent]]

%% ai-graph-end %%