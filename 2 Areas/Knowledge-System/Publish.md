---
ai_hash: 8d47a6f106fa373d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Publish
- Recording 20260527091708.m4a
- /c/quartz/publish.sh
- vault
- Quartz
- Makefile
- make publish
- shell
- alias
- auto-msg
- custom message
- Vault dirty
- Vault clean but submodule behind
- Everything already in sync
- C:\quartz
---

![[Recording 20260527091708.m4a]]
## Usage

```bash
# from anywhere
/c/quartz/publish.sh                       # auto-msg: "vault update 2026-05-25 23:07"
/c/quartz/publish.sh "add stage 7 notes"   # custom message, used for both repos
```

It handles three states cleanly:

- Vault dirty → commits & pushes vault, bumps submodule, pushes Quartz.
- Vault clean but submodule behind → bumps submodule, pushes Quartz.
- Everything already in sync → prints "nothing to publish" and exits 0.

Optional polish if you want it later (not doing now): drop a `Makefile` with `make publish` in `C:\quartz`, or alias it in your shell (`alias publish='/c/quartz/publish.sh'`). Say the word.

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

**Relations:**
- Publish — *references* — Recording 20260527091708.m4a
- /c/quartz/publish.sh — *is used for* — Publish
- /c/quartz/publish.sh — *accepts argument* — auto-msg
- /c/quartz/publish.sh — *accepts argument* — custom message
- /c/quartz/publish.sh — *handles state* — Vault dirty
- /c/quartz/publish.sh — *handles state* — Vault clean but submodule behind
- /c/quartz/publish.sh — *handles state* — Everything already in sync
- Vault dirty — *triggers action* — commits & pushes vault
- Vault dirty — *triggers action* — bumps submodule
- Vault dirty — *triggers action* — pushes Quartz
- Vault clean but submodule behind — *triggers action* — bumps submodule
- Vault clean but submodule behind — *triggers action* — pushes Quartz
- Everything already in sync — *results in* — prints 'nothing to publish'
- Makefile — *can define command* — make publish
- make publish — *is an alternative to* — /c/quartz/publish.sh
- alias — *can be created for* — /c/quartz/publish.sh
- alias — *can be named* — publish
- alias — *is configured in* — shell
- /c/quartz/publish.sh — *is located in* — C:\quartz
- vault — *is a submodule of* — Quartz

%% ai-graph-end %%