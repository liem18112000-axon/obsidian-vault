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