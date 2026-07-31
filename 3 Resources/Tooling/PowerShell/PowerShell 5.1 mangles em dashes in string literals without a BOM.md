---
title: "PowerShell 5.1 mangles em dashes in string literals without a BOM"
created: 2026-07-07
type: lesson
status: seedling
source: "vinnstack session 2026-07-07"
tags: [powershell, encoding, gotcha, windows]
---

# PowerShell 5.1 mangles em dashes in string literals without a BOM

Windows PowerShell 5.1 (powershell.exe) reads a `.ps1` file without a byte-order mark using the system ANSI codepage, not UTF-8 - so a multibyte UTF-8 character sitting inside an actual double-quoted string literal in executable code can get mis-decoded into bytes PowerShell reads as a string terminator, breaking the parse.

**Symptom:** a script that looked fine failed with `ParseException: The string is missing the terminator` / `Missing closing '}' in statement block`, pointing at a line containing an em dash (`—`) inside a `Fail "..."` string.

**Why comments were unaffected:** an em dash a few lines earlier, inside a `<# ... #>` comment block, caused no error - comment blocks are scanned for the closing `#>` marker, not for balanced quotes, so mis-decoded bytes inside them are harmless.

**Rule:** keep non-ASCII punctuation (em dashes, smart quotes, curly apostrophes, etc.) out of PowerShell string literals in executable code. Use plain ASCII (`-` or `--`) there, and reserve em dashes for comments/docs.

**Where this bit:** writing `scripts/build-exe.ps1` in the vinnstack repo (Windows, PowerShell 5.1) - a file created via a UTF-8-writing tool (no BOM). Existing `.ps1` scripts in the same repo (e.g. `connect-cloud-db.ps1`) also contain em dashes, but only inside comment blocks, which is why they never hit this.

## Related
[[PowerShell scripting conventions]]
