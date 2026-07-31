---
title: "GitHub Actions artifact storage quota: use short retention-days on handoff artifacts"
created: 2026-06-21
type: lesson
status: seedling
source: "session 2026-06-21, fb-info-project"
tags: [github-actions, ci, gotcha, storage]
---

# GitHub Actions artifact storage quota: use short retention-days on handoff artifacts

Workflows that upload large artifacts on every push will eventually hit the **Actions artifact storage quota** (`Failed to CreateArtifact: Artifact storage quota has been hit`), because the default artifact retention is **90 days** and copies accumulate. The worst offenders are big bundles — e.g. a PyInstaller one-dir build with Chromium baked in (hundreds of MB).

**Fix — set `retention-days` low on handoff artifacts.** An artifact that only exists to pass a file from one job to another (build → test) does not need to live 90 days; set `retention-days: 1`.

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: my-bundle
    path: my-bundle.zip
    retention-days: 1
```

**Key distinction:** a zip attached to a GitHub **Release** (via `softprops/action-gh-release`) is stored *separately* from Actions artifacts and does **not** count against the artifact quota. So the durable downloadable copy can live on the Releases page while the Actions artifact expires fast.

**Unblock immediately** when the quota is already full — deleting Actions artifacts does NOT touch Release assets:

```bash
gh api repos/{owner}/{repo}/actions/artifacts --paginate --jq '.artifacts[].id'   | xargs -I{} gh api -X DELETE repos/{owner}/{repo}/actions/artifacts/{}
```

Storage usage recalculates every 6–12 h, so it may not drop instantly even after deletion.

Seen in fb-info-project `.github/workflows/build-exe.yml`.

## Related

- [[GitHub Actions Node 20 deprecation warning from v4 actions is harmless noise]]
