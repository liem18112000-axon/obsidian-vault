---
ai_hash: 1401442884d4d709
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05
status: seedling
tags:
- claude-code
- hooks
- git
- regex
- gotcha
title: Command-scanning git-commit hooks miss flag-separated forms and -F message
  files
type: lesson
---

# Command-scanning git-commit hooks miss flag-separated forms and -F message files

A PreToolUse hook that blocks commits by regex-scanning the `git commit` command string has two structural gaps beyond whatever pattern it checks.

1. **Flag-separated invocations.** `\bgit\s+commit\b` only matches when `commit` immediately follows `git`, so `git -C <path> commit` or `git -c key=val commit` sail through. Match `\bgit\b[^|;&]*\bcommit\b` instead — the `[^|;&]*` allows intervening flags but stops at command separators, so `git log | grep x; svn commit` does not false-positive.

2. **Message files are invisible.** `git commit -F file` / `--file` puts the message on disk, not in the command string — no command-scanning hook can see it. Catching that requires a real git `commit-msg` hook in the repo, which inspects the final message regardless of how it was supplied.

Accept the small false-positive cost: a legit message that merely *mentions* the blocked phrase (e.g. docs about the policy) will be denied — usually the right trade for an enforcement hook.

Verified 2026-06-05 against `~/.claude/hooks/git/block-coauthor.ps1` with a 7-case harness piping JSON payloads into the script.

## Related

- [[3 Resources/AI/Claude-Code/Hooks/Block AI commit attribution by anchoring on the email, not the trailer label]]

%% ai-graph-start %%

**Related notes:**
- [[Block AI commit attribution by anchoring on the email, not the trailer label]]
- [[Regex allowlists of model names go stale when vendors ship new names]]
- [[PowerShell here-string @'...'@ silently corrupts git commit messages in the Bash tool]]
- [[Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]
- [[Pre-staged files silently merge selective commit batches - check the index first]]

%% ai-graph-end %%