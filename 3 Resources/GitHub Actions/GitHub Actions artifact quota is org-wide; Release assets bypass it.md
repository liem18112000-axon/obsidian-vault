---
title: "GitHub Actions artifact quota is org-wide; Release assets bypass it"
created: 2026-06-19
type: lesson
status: seedling
source: "session 2026-06-19"
tags: [github-actions, ci, gotcha, artifacts, releases]
---

# GitHub Actions artifact quota is org-wide; Release assets bypass it

GitHub Actions **artifact storage** (`actions/upload-artifact`) shares one quota **across the whole account/org**, not per-repo. When it fills, uploads fail with `Failed to CreateArtifact: Artifact storage quota has been hit` even if your own repo holds almost nothing. Usage is only recalculated every 6-12 hours, so deleting artifacts gives delayed relief.

**Key distinction:** **GitHub Release assets do NOT count against the Actions artifact-storage quota** (they are separate release storage, free for public repos). So for release-style delivery, attach the file to the Release instead of relying on an uploaded artifact.

**Gotcha that bites:** if a separate `release` job has `needs: build` and the build job's *artifact upload* step fails on quota, the build job is marked failed and the release job is skipped — so delivery is gated on the very quota you were trying to bypass. Fix: attach the asset to the Release **from the build job itself** (e.g. `softprops/action-gh-release@v2` with `if: startsWith(github.ref, 'refs/tags/v')`), and mark the `upload-artifact` step `continue-on-error: true` so it stays a best-effort convenience for non-tag runs. Requires `permissions: contents: write`.

Encountered building the HoSoBaiGiang offline Windows bundle workflow.
