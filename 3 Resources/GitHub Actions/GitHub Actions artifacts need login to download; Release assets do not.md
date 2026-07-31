---
title: "GitHub Actions artifacts need login to download; Release assets do not"
created: 2026-06-17
type: lesson
status: seedling
source: "session 2026-06-17"
tags: [github, actions, artifacts, release]
---

# GitHub Actions artifacts need login to download; Release assets do not

Two ways to publish a build from GitHub CI, with different access rules:

- **Actions artifacts** (`actions/upload-artifact`, ~10 GB cap): only a **logged-in user with repo access** can download them, and they expire (default 90 days). Fine for your team; useless for an external client with just a link.
- **Release assets**: on a **public repo** they download with **no login**. Permanent. But each file is capped at 2 GiB — see [[GitHub Release assets are capped at 2 GiB per file]].

**Rule of thumb:** pick artifact vs Release by *audience* — internal/team => artifact; external client => Release.

## Related

- [[GitHub Release assets are capped at 2 GiB per file]]
