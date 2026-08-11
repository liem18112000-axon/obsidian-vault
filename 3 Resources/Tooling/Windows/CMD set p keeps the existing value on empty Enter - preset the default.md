---
ai_hash: c0d38ba33b25a279
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project license_admin.cmd
status: seedling
tags:
- cmd
- batch
- windows
- gotcha
title: CMD set /p keeps the existing value on empty Enter - preset the default
type: lesson
---

# CMD set /p keeps the existing value on empty Enter - preset the default

In Windows batch, `set /p VAR=prompt` leaves VAR untouched when the user presses Enter without typing anything - it does NOT clear the variable. So assigning a default immediately before the prompt gives default-on-Enter behavior with no follow-up `if` check:

```bat
set "TIER=standard"
set /p TIER=Tier trial/standard/pro (Enter = standard):
```

If the user types a value, it replaces the default; if they just press Enter, `standard` survives. The flip side is a gotcha: when you want a *mandatory* input, you must explicitly clear first (`set "PRIVKEY="`) or a stale value from an earlier prompt/environment leaks through.

Related: [[Dual-mode CMD wrapper - args pass through, no args opens an interactive menu]]

%% ai-graph-start %%

**Related notes:**
- [[Dual-mode CMD wrapper - args pass through, no args opens an interactive menu]]

%% ai-graph-end %%