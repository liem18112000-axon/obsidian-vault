---
ai_hash: 9ce09626ef790e96
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: code review 2026-07-01 (vinnstack)
status: seedling
tags:
- nodejs
- windows
- environment-variables
- gotcha
title: Node.js process.env is case-insensitive on Windows
type: term
---

# Node.js process.env is case-insensitive on Windows

On Windows, Node.js exposes `process.env` through a **case-insensitive** proxy: `process.env.PATH`, `process.env.Path`, and `process.env.path` all return the same value regardless of the variable's actual casing in the OS.

**Consequence:** defensive fallbacks like `process.env.PATH || process.env.Path` are **dead code** on win32 — the second operand can never be reached with a different value. Write just `process.env.PATH`.

**Contrast:** on POSIX (Linux/macOS) environment variables *are* case-sensitive, so `PATH` and `Path` are genuinely different keys there. Code that must run cross-platform should still normalize to the canonical name rather than OR-ing casings.

Surfaced reviewing `resolveClaudeBin()` in vinnstack, which split `(process.env.PATH || process.env.Path || "")` on a Windows-only path.

Related: [[Never cache a negative fallback in the same slot as a resolved value]] (same function).

## Related

- [[Never cache a negative fallback in the same slot as a resolved value]]

%% ai-graph-start %%

**Related notes:**
- [[Never cache a negative fallback in the same slot as a resolved value]]
- [[Windows setx does not update already-running processes' environment]]
- [[Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]
- [[spawn python ENOENT on Windows — resolve a real interpreter, not the Store alias]]
- [[A silent canned fallback masks real failures — surface the underlying error]]

%% ai-graph-end %%