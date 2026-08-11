---
ai_hash: 5086da617616bc0c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-05
entities: []
source: vinnstack session 2026-07-05
status: seedling
tags:
- git
- bash
- powershell
- commit
- gotcha
title: PowerShell here-string @'...'@ silently corrupts git commit messages in the
  Bash tool
type: gotcha
---

# PowerShell here-string @'...'@ silently corrupts git commit messages in the Bash tool

The Bash tool runs POSIX sh, NOT PowerShell. Passing a commit message as a PowerShell here-string — git commit -m @'...body...'@ — does NOT do what it looks like:
- sh parses @'...'@ as: literal @ + a single-quoted string + literal @. So the message gets a stray leading/trailing @ (shows up as "@ Subject" in git log).
- Worse: any apostrophe inside the body (e.g. "Story's", "PRD's") CLOSES the single quote early. The rest of the body is then parsed as shell commands, so the commit is created with a TRUNCATED message and you get "command not found" / "syntax error near unexpected token (" noise after a commit that already succeeded.

Symptom: git log subject line starts with "@", and multi-paragraph bodies are cut off at the first apostrophe.

Correct in the Bash tool: use a quoted heredoc — git commit -F - <<'EOF' ... EOF (the quoted 'EOF' prevents $ and backtick expansion, apostrophes are safe). Or write the message to a temp file and git commit -F file. The @'...'@ form is only valid in the PowerShell tool, where it is the right way to pass multi-line messages. Pick the here-doc form that matches the shell the tool actually runs.

%% ai-graph-start %%

**Related notes:**
- [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]]
- [[Command-scanning git-commit hooks miss flag-separated forms and -F message files]]
- [[Windows PowerShell 5.1 reads BOM-less scripts as ANSI, breaking on em-dashes]]
- [[Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON]]
- [[PowerShell pipe appends a newline to native-command stdin, shifting any hash]]

%% ai-graph-end %%