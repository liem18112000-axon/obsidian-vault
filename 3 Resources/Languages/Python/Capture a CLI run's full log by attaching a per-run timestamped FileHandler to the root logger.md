---
title: "Capture a CLI run's full log by attaching a per-run timestamped FileHandler to the root logger"
created: 2026-06-23
type: lesson
status: seedling
source: "session 2026-06-23"
tags: [python, logging, cli, gotcha]
---

# Capture a CLI run's full log by attaching a per-run timestamped FileHandler to the root logger

To capture a CLI run's full log to a fresh file per run while keeping console output, in setup: build a per-run filename with a timestamp postfix (logs/run_<YYYYmmdd_HHMMSS>.log) and attach a FileHandler to the **root** logger — not just your app logger. Your app logger's records propagate up to root, and so do third-party libraries' (e.g. scrapling), so the single file captures the whole run rather than only your own lines. Roll over (new file) each run instead of appending, so runs stay separate.

Gotcha: a propagated record is gated by the ORIGIN logger's effective level, not root's. A library logging at INFO only lands in the file if that library set its own logger to INFO (most do). A synthetic test logger with no level inherits root=WARNING and its INFO is dropped — so test with the real logger, not a fake one.

Also: on re-init, remove the previous FileHandler from root (handler.close()) before adding the new one, or one process writes to two run files. And gitignore the logs/ dir; if a stale log file was already committed, `git rm --cached` it (keeps the file on disk) so the ignore actually takes effect — .gitignore never untracks already-tracked files. Instance: fb-info-project src/logging_setup.py.

## Related

- [[fb-info-project merges bare-id 'profile UUID' inputs into one combined workbook]]
