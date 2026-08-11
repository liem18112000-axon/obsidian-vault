---
ai_hash: d763cc9703b3a13c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03 appsflyer-data-connector
status: seedling
tags:
- python
- refactoring
- tokenize
- ast
title: Strip all comments and docstrings from Python safely with tokenize plus AST
type: howto
---

# Strip all comments and docstrings from Python safely with tokenize plus AST

To mechanically remove every comment and docstring from Python without touching behavior, combine three exact tools instead of regex:

1. **AST for docstrings** — walk the tree; a docstring is the first statement of a Module/ClassDef/FunctionDef when it is an `Expr(Constant(str))`. Blank its `lineno..end_lineno` range. If a class/function body was ONLY the docstring, write `pass` at the docstring's `col_offset` (e.g. an exception class defined by its docstring alone would otherwise become a SyntaxError).
2. **tokenize for `#` comments** — regex cannot tell a comment from a `#` inside a string literal; `tokenize.generate_tokens` can. For each COMMENT token, keep `line[:col]` (rstripped) or drop the line if nothing precedes it.
3. **`compile(result, path, 'exec')` as a per-file guard** — catches any stripping bug at transform time, before tests even run.

Then collapse blank-line runs (2 before column-0 statements, 1 inside blocks). Two follow-ups surfaced in practice: check the linter config FIRST — deleting `# noqa` markers is only safe if those rules aren't enabled (ruff's default set is just E4/E7/E9/F, so noqa was dead weight here); and the naive collapse rule leaves double blanks between import groups (each import is a col-0 statement) — one extra regex pass fixes it. Applied to appsflyer-data-connector src/: 38 files, ~430 lines removed, ruff + 155 tests green.

## Related

- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]

%% ai-graph-start %%

**Related notes:**
- [[Convert a Python module to a package without breaking importers via re-exporting __init__]]

%% ai-graph-end %%