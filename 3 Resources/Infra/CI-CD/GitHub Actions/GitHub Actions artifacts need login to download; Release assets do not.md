---
ai_hash: ad046f82325d400a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: session 2026-06-17
status: seedling
tags:
- github
- actions
- artifacts
- release
title: GitHub Actions artifacts need login to download; Release assets do not
type: lesson
---

# GitHub Actions artifacts need login to download; Release assets do not

Two ways to publish a build from GitHub CI, with different access rules:

- **Actions artifacts** (`actions/upload-artifact`, ~10 GB cap): only a **logged-in user with repo access** can download them, and they expire (default 90 days). Fine for your team; useless for an external client with just a link.
- **Release assets**: on a **public repo** they download with **no login**. Permanent. But each file is capped at 2 GiB — see [[GitHub Release assets are capped at 2 GiB per file]].

**Rule of thumb:** pick artifact vs Release by *audience* — internal/team => artifact; external client => Release.

## Related

- [[GitHub Release assets are capped at 2 GiB per file]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Actions artifact quota is org-wide; Release assets bypass it]]
- [[Publish every CI build via a rolling latest pre-release on GitHub]]
- [[GitHub Release assets are capped at 2 GiB per file]]
- [[Cloud Run can only pull images from Artifact Registry or GCR, not GHCR]]
- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]

%% ai-graph-end %%