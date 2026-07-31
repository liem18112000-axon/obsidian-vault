---
title: "CMD set /p keeps the existing value on empty Enter - preset the default"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04 fb-info-project license_admin.cmd"
tags: [cmd, batch, windows, gotcha]
---

# CMD set /p keeps the existing value on empty Enter - preset the default

In Windows batch, `set /p VAR=prompt` leaves VAR untouched when the user presses Enter without typing anything - it does NOT clear the variable. So assigning a default immediately before the prompt gives default-on-Enter behavior with no follow-up `if` check:

```bat
set "TIER=standard"
set /p TIER=Tier trial/standard/pro (Enter = standard):
```

If the user types a value, it replaces the default; if they just press Enter, `standard` survives. The flip side is a gotcha: when you want a *mandatory* input, you must explicitly clear first (`set "PRIVKEY="`) or a stale value from an earlier prompt/environment leaks through.

Related: [[Dual-mode CMD wrapper - args pass through, no args opens an interactive menu]]
