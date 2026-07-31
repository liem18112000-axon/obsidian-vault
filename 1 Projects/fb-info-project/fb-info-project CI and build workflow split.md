---
ai_hash: 53a7a9d18f1d11a2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-05
entities:
- fb-info-project
- GitHub Actions
- ci.yml
- push to master
- PRs
- source-only offline tests
- deps
- '`src/`-importing `*_unit.py` tests'
- PyInstaller exe
- black-box tiers
- Black-box exe test suite skips silently when no artifact is present
- build-exe.yml
- '`v*` tag'
- manual dispatch
- exe
- '`build.cmd --no-sign`'
- release
- per-user license token
- user dispatch input
- '`build.cmd sign`'
- LICENSE_PRIVKEY secret
- pubkey-match check
- masked short-retention artifact
- sign-license.yml
- token signing
- build.cmd
- .venv
- final `pause`
- CI
- black-box e2e
- local-only
- build.cmd is the single build entrypoint reused by CI and local
- local
- releases
- no workflow
source: fb-info-project session 2026-07-05
status: seedling
tags:
- fb-info-project
- github-actions
- ci
- licensing
- build
title: fb-info-project CI and build workflow split
type: model
---

# fb-info-project CI and build workflow split

fb-info-project splits its GitHub Actions across three workflows by **actionability of the trigger**, so fast validation and slow release/signing don't block each other.

- **`ci.yml`** (push to master + PRs) — source-only offline tests: installs deps and runs the `src/`-importing `*_unit.py` tests. Does **not** build the PyInstaller exe, so it's fast; consequently the black-box tiers are skipped here (see [[Black-box exe test suite skips silently when no artifact is present]]).
- **`build-exe.yml`** (`v*` tag or manual dispatch) — builds the exe via `build.cmd --no-sign`, zips it, attaches to a release, and — when a `user` dispatch input is given — signs a per-user license token by reusing `build.cmd sign` + the `LICENSE_PRIVKEY` secret, guarded by a pubkey-match check and delivered as a masked short-retention artifact.
- **`sign-license.yml`** — standalone, build-free token signing.

**Key decisions & why:** `build.cmd` is the single build entrypoint (CI, releases, and local all call it, so they can't drift); it activates `.venv` only if present and skips its final `pause` when `CI` is set, so the same script is safe interactively and in Actions. Black-box e2e against the packaged exe intentionally runs **local-only** (via `build.cmd`) — no workflow runs it automatically.

Related: [[build.cmd is the single build entrypoint reused by CI and local]]

## Related

- [[Black-box exe test suite skips silently when no artifact is present]]

%% ai-graph-start %%

**Related notes:**
- [[Black-box exe test suite skips silently when no artifact is present]]
- [[Black-box artifact E2E drive a committed sample fixture so the only live input is the secret]]
- [[Offline license verification - the trust anchor must be baked into the binary, never runtime-configurable]]
- [[Operator-only config can be externalized to a data file with no client rebuild]]
- [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]

**Relations:**
- fb-info-project — *splits* — GitHub Actions
- GitHub Actions — *includes_workflow* — ci.yml
- GitHub Actions — *includes_workflow* — build-exe.yml
- GitHub Actions — *includes_workflow* — sign-license.yml
- ci.yml — *triggered_by* — push to master
- ci.yml — *triggered_by* — PRs
- ci.yml — *runs* — source-only offline tests
- ci.yml — *installs* — deps
- ci.yml — *runs* — `src/`-importing `*_unit.py` tests
- ci.yml — *does_not_build* — PyInstaller exe
- ci.yml — *skips* — black-box tiers
- black-box tiers — *related_to* — Black-box exe test suite skips silently when no artifact is present
- build-exe.yml — *triggered_by* — `v*` tag
- build-exe.yml — *triggered_by* — manual dispatch
- build-exe.yml — *builds* — exe
- build-exe.yml — *uses_command* — `build.cmd --no-sign`
- build-exe.yml — *zips* — exe
- build-exe.yml — *attaches_to* — release
- build-exe.yml — *signs* — per-user license token
- per-user license token — *signed_when_input_given* — user dispatch input
- build-exe.yml — *re_uses_command* — `build.cmd sign`
- `build.cmd sign` — *uses* — LICENSE_PRIVKEY secret
- LICENSE_PRIVKEY secret — *guarded_by* — pubkey-match check
- per-user license token — *delivered_as* — masked short-retention artifact
- sign-license.yml — *is* — standalone
- sign-license.yml — *is* — build-free
- sign-license.yml — *performs* — token signing
- build.cmd — *is_entrypoint_for* — CI
- build.cmd — *is_entrypoint_for* — releases
- build.cmd — *is_entrypoint_for* — local
- build.cmd — *activates* — .venv
- build.cmd — *skips* — final `pause`
- final `pause` — *when_set* — CI
- black-box e2e — *runs* — local-only
- black-box e2e — *runs_via* — build.cmd
- no workflow — *automatically_runs* — black-box e2e
- build.cmd — *related_to* — build.cmd is the single build entrypoint reused by CI and local

%% ai-graph-end %%