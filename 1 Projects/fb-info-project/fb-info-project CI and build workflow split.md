---
title: "fb-info-project CI and build workflow split"
created: 2026-07-05
type: model
status: seedling
source: "fb-info-project session 2026-07-05"
tags: [fb-info-project, github-actions, ci, licensing, build]
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
