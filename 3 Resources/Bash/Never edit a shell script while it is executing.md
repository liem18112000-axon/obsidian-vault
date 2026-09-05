---
title: "Never edit a shell script while it is executing"
created: 2026-08-07
type: lesson
status: seedling
source: "session 2026-08-07 leo-customer360 up.sh"
tags: [bash, gotcha, shell, local-dev]
---

# Never edit a shell script while it is executing

Bash parses and executes a script by reading it incrementally from the file by **byte offset** — it does not load the whole script into memory first. So if you edit the file while it is running (especially inserting or deleting lines *above* the point bash has not reached yet), every byte after the edit shifts position. When bash resumes reading after a long-running child command finishes, it seeks to its remembered offset and lands mid-line, producing a bogus `syntax error near unexpected token` on a line that is actually correct on disk.

**Symptom that gives it away:** the on-disk script is valid, the error points at an innocuous later line (e.g. a `(...)` inside a `cat <<'EOF'` help block), and it only happens on a run during which you edited the file.

**Avoid it:** finish (or kill) the run before editing, or copy the script to a temp path and run the copy while you edit the original. Re-running the corrected script cleanly succeeds.

Surfaced while fixing `leo-customer360` `k8s/scripts/up.sh`: images all built and loaded, then the resumed parent script died at "line 44" inside the trailing heredoc purely because the file had been edited mid-run.

## Related
[[kind lists a cluster even when its node container is stopped]]

## Related

- [[kind lists a cluster even when its node container is stopped]]
