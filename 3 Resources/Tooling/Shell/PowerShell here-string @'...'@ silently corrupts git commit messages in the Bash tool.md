---
title: "PowerShell here-string @'...'@ silently corrupts git commit messages in the Bash tool"
created: 2026-07-05
type: gotcha
status: seedling
source: "vinnstack session 2026-07-05"
tags: [git, bash, powershell, commit, gotcha]
---

# PowerShell here-string @'...'@ silently corrupts git commit messages in the Bash tool

The Bash tool runs POSIX sh, NOT PowerShell. Passing a commit message as a PowerShell here-string — git commit -m @'...body...'@ — does NOT do what it looks like:
- sh parses @'...'@ as: literal @ + a single-quoted string + literal @. So the message gets a stray leading/trailing @ (shows up as "@ Subject" in git log).
- Worse: any apostrophe inside the body (e.g. "Story's", "PRD's") CLOSES the single quote early. The rest of the body is then parsed as shell commands, so the commit is created with a TRUNCATED message and you get "command not found" / "syntax error near unexpected token (" noise after a commit that already succeeded.

Symptom: git log subject line starts with "@", and multi-paragraph bodies are cut off at the first apostrophe.

Correct in the Bash tool: use a quoted heredoc — git commit -F - <<'EOF' ... EOF (the quoted 'EOF' prevents $ and backtick expansion, apostrophes are safe). Or write the message to a temp file and git commit -F file. The @'...'@ form is only valid in the PowerShell tool, where it is the right way to pass multi-line messages. Pick the here-doc form that matches the shell the tool actually runs.
