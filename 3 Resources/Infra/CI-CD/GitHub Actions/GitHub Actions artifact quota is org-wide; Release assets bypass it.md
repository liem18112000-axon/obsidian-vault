---
ai_hash: f91428472c84a4fb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19 HoSoBaiGiang; session 2026-06-21 fb-info-project build-exe.yml
status: seedling
tags:
- github-actions
- ci
- gotcha
- artifacts
- releases
- storage
title: GitHub Actions artifact quota is org-wide; Release assets bypass it
type: lesson
updated: 2026-07-31
---

# GitHub Actions artifact quota is org-wide; Release assets bypass it

`actions/upload-artifact` storage shares **one quota across the whole account/org**, not per-repo. When it fills, uploads fail with `Failed to CreateArtifact: Artifact storage quota has been hit` even if your own repo holds almost nothing. Usage recalculates only every 6–12 h, so deletions give delayed relief.

**Key distinction: GitHub Release assets do NOT count against the Actions artifact quota** (separate release storage, free for public repos). Durable downloadable copy → Release; ephemeral job-to-job handoff → artifact.

**Fix 1 — short `retention-days` on handoff artifacts.** Default retention is **90 days**, so big bundles (e.g. a PyInstaller one-dir build with Chromium baked in) accumulate copies on every push. An artifact that only passes a file build → test does not need 90 days:

```yaml
- uses: actions/upload-artifact@v4
  with: { name: my-bundle, path: my-bundle.zip, retention-days: 1 }
```

**Fix 2 — publish via the Release, from the build job itself.** Gotcha: if a separate `release` job has `needs: build` and the build's upload step fails on quota, the build is marked failed and the release job is skipped — delivery gated on the very quota you were bypassing. Instead run `softprops/action-gh-release@v2` (with `if: startsWith(github.ref, 'refs/tags/v')`, `permissions: contents: write`) inside the build job, and set `continue-on-error: true` on `upload-artifact` so it stays best-effort.

**Unblock a full quota now** (does not touch Release assets):

```bash
gh api repos/{owner}/{repo}/actions/artifacts --paginate --jq '.artifacts[].id' \
  | xargs -I{} gh api -X DELETE repos/{owner}/{repo}/actions/artifacts/{}
```

## Related

- [[GitHub Actions artifacts need login to download; Release assets do not]]
- [[GitHub Release assets are capped at 2 GiB per file]]
- [[GitHub Actions Node 20 deprecation warning from v4 actions is harmless noise]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Actions artifacts need login to download; Release assets do not]]
- [[GitHub Release assets are capped at 2 GiB per file]]
- [[Publish every CI build via a rolling latest pre-release on GitHub]]
- [[GitHub Actions Node 20 deprecation warning from v4 actions is harmless noise]]
- [[GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags]]

%% ai-graph-end %%