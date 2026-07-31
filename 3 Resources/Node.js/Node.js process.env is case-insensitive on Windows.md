---
title: "Node.js process.env is case-insensitive on Windows"
created: 2026-07-01
type: term
status: seedling
source: "code review 2026-07-01 (vinnstack)"
tags: [nodejs, windows, environment-variables, gotcha]
---

# Node.js process.env is case-insensitive on Windows

On Windows, Node.js exposes `process.env` through a **case-insensitive** proxy: `process.env.PATH`, `process.env.Path`, and `process.env.path` all return the same value regardless of the variable's actual casing in the OS.

**Consequence:** defensive fallbacks like `process.env.PATH || process.env.Path` are **dead code** on win32 — the second operand can never be reached with a different value. Write just `process.env.PATH`.

**Contrast:** on POSIX (Linux/macOS) environment variables *are* case-sensitive, so `PATH` and `Path` are genuinely different keys there. Code that must run cross-platform should still normalize to the canonical name rather than OR-ing casings.

Surfaced reviewing `resolveClaudeBin()` in vinnstack, which split `(process.env.PATH || process.env.Path || "")` on a Windows-only path.

Related: [[Never cache a negative fallback in the same slot as a resolved value]] (same function).

## Related

- [[Never cache a negative fallback in the same slot as a resolved value]]
