---
title: "Git Bash mktemp paths are unreadable by Windows python; pipe via stdin instead of a temp-file path"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 leo-customer360 deploy scripts"
tags: [git-bash, msys, windows, python, shell, gotcha]
---

# Git Bash mktemp paths are unreadable by Windows python; pipe via stdin instead of a temp-file path

In Git Bash / MSYS on Windows, `mktemp` returns an MSYS-style path like `/tmp/tmp.XXptmp`. If you then hand that path to the **Windows** `python3` (or any native-Windows tool), it fails with `FileNotFoundError: /tmp/tmp.XX` — native tools do not understand the MSYS `/tmp` mount. Symptom: a bash script writes `terraform output -json > "$(mktemp)"` then `python3 -c "open(sys.argv[1])"` and python cant find the file.

Fix: dont pass an MSYS temp-file PATH to a native tool. Instead pipe the data over **stdin** and pass parameters via **argv**, e.g.
  DATA="$(some-cmd)"; printf %s "$DATA" | python3 -c "import json,sys; d=json.load(sys.stdin); ..." "$key" "$field"
stdin + argv cross the bash->native boundary fine; only filesystem paths get mistranslated. (Alternatively use `cygpath -w` to convert the path, or write to `./` a relative file, but stdin is simplest.)
