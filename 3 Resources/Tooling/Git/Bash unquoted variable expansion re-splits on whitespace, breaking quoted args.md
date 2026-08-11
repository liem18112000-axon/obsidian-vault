---
ai_hash: 29951a5542ba571a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: Vinnstack BDD verify-stage work, 2026-07-12
status: seedling
tags:
- bash
- shell-scripting
- gotcha
- word-splitting
title: Bash unquoted variable expansion re-splits on whitespace, breaking quoted args
type: lesson
---

# Bash unquoted variable expansion re-splits on whitespace, breaking quoted args

In bash, once a variable holding a shell-command-like string is expanded UNQUOTED (e.g. `$behave_args` inside `"$PY" -m behave $behave_args`), the shell re-splits the expanded text on IFS whitespace — literal quote characters that were part of the string do NOT get re-interpreted as grouping. So a variable containing `--name "Some Title"` becomes three separate argv words (`--name`, `"Some`, `Title"`) once expanded unquoted, not one flag + one quoted value as a human reading the string would expect. Quotes only group words during the shells OWN initial parsing of a command line — they carry no special meaning during a subsequent unquoted variable expansion.

**Fix options:**
- Build the argument list as a bash ARRAY from the start (`args=(--name "$title")`) and expand it with `"${args[@]}"` — each element survives expansion as exactly one argv word regardless of embedded spaces.
- Or `eval` the string to force a second round of shell parsing — but this reintroduces injection risk if any part of the string is not fully trusted, so prefer the array approach.

Found this auditing a pre-existing bash IT-runner script (`run_it.sh`, luz-docs-integration-test skill) that built its final behave invocation via `"$PY" -m behave $behave_args` — worked fine for simple flag-only CMD strings, but would silently mangle any attempt to target one scenario by `--name "<title with spaces>"`. Fixed in a Vinnstack-local fork by appending scenario/feature-path targeting as separate array elements instead of concatenating into the CMD string.

%% ai-graph-start %%

**Related notes:**
- [[behave step patterns differing only by quote style are distinct definitions]]
- [[run_it.sh TENANT_ID is a shell parameter, not an .env value]]

%% ai-graph-end %%