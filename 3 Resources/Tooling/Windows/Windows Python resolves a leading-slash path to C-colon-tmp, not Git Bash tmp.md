---
title: "Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp"
created: 2026-07-31
type: gotcha
status: seedling
source: "vault PARA reorganization 2026-07-31"
tags: [windows, git-bash, python, paths, gotcha]
---

# Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp

A Windows-native Python interpreter treats a leading-slash path as **drive-relative on the current drive**, so `/tmp/out.json` becomes `C:\tmp\out.json`. Git Bash maps `/tmp` to `%LOCALAPPDATA%\Temp` instead. Run a Windows Python script from a Git Bash shell and the two disagree about where the file went.

The failure is silent and confusing: the script reports success, `ls /tmp/out.json` from Bash says "No such file or directory", and it looks as though the write failed.

Ways out:

- convert at the call site — `python script.py "$(cygpath -w /tmp/vaultbase)"`
- pass absolute Windows paths (`C:/tmp/out.json`) and be explicit that that is a different directory from Bash `/tmp`
- inside Python, prefer `tempfile.gettempdir()` over a hardcoded `/tmp`

Same class of bug applies to any Windows-native tool invoked from Git Bash — the shell rewrites some arguments that look like paths, but a leading-slash string handed to the program untouched is interpreted by Windows rules.

## Related

- [[A charmap UnicodeEncodeError can kill a script before it writes its output file]]
