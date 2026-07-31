---
title: "argparse help with non-cp1252 chars crashes on Windows consoles"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [python, argparse, windows, encoding, cp1252]
---

# argparse help with non-cp1252 chars crashes on Windows consoles

Gotcha: on Windows, `argparse --help` crashes with `UnicodeEncodeError: charmap codec cannot encode character` when a help string contains a character outside cp1252 — e.g. "→" (U+2192). The em-dash "—" survives (cp1252 has it at 0x97), which makes the failure look random: one CLI prints help fine, another dies. The entry point/import is fine; only printing help to a cp1252 console explodes, so tests never catch it. Fix: keep argparse help strings ASCII (use "->"), or set PYTHONIOENCODING=utf-8 / UTF-8 console. Surfaced in leo-appsflyer-gen after the pyproject refactor; found only because the verification actually executed every console script.

Related: [[Grep-audit env vars against code before pruning .env files]]

## Related

- [[Grep-audit env vars against code before pruning .env files]]
