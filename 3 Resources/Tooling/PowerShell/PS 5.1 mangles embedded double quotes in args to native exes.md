---
title: "PS 5.1 mangles embedded double quotes in args to native exes"
created: 2026-07-03
type: gotcha
status: seedling
source: "session 2026-07-03, vinnstack git commit failure"
tags: [powershell, windows, git]
---

# PS 5.1 mangles embedded double quotes in args to native exes

In Windows PowerShell 5.1, passing a string that CONTAINS double quotes to a native executable (git, node, ...) mangles the argument: PS re-quotes the arg for the native command line but does not escape embedded `"`, so the argument splits at the first inner quote. A `git commit -m @'...multi-line with "quoted words"...'@` here-string parses fine in PS yet reaches git as several broken pathspec args - git then errors with `pathspec '<word-after-quote>' did not match any file(s)` and NO commit is created.

Fix: strip or replace double quotes in strings destined for native-exe arguments (single quotes render fine), or use `--%` stop-parsing / a temp file (`git commit -F msg.txt`). Recognize the symptom: pathspec errors naming words from the middle of your message.
