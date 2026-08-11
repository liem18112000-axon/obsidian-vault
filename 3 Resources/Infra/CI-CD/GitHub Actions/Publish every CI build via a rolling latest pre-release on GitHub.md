---
ai_hash: 6615ab9752bb63a3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: fb-info-project build workflow, session 2026-06-10
status: seedling
tags:
- github-actions
- ci
- release
title: Publish every CI build via a rolling latest pre-release on GitHub
type: howto
---

# Publish every CI build via a rolling latest pre-release on GitHub

GitHub Releases must hang off a tag, so you cannot attach a build asset to "a commit on master" directly. The standard pattern for making every default-branch build publicly downloadable is a rolling pre-release on a fixed tag (conventionally `latest`), while real version tags keep their own permanent releases:

```yaml
- uses: softprops/action-gh-release@v2
  with:
    tag_name: ${{ startsWith(github.ref, 'refs/tags/') && github.ref_name || 'latest' }}
    prerelease: ${{ !startsWith(github.ref, 'refs/tags/') }}
    files: app.zip
```

action-gh-release reuses the existing `latest` release and replaces same-named assets, so the download URL stays stable and always serves the newest build. The `a && b || c` expression is the GitHub Actions idiom for ternary (safe while `b` is truthy).

Caveat: on subsequent runs the `latest` tag itself keeps pointing at the commit that first created the release — only the assets are refreshed. If the tag must track HEAD, delete the release+tag before re-publishing.

Related: [[GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags]]

## Related

- [[3 Resources/Infra/CI-CD/GitHub Actions/GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Actions artifacts need login to download; Release assets do not]]
- [[GitHub Actions push filters - tags-only skips branch pushes, paths ignored for tags]]
- [[GitHub Actions artifact quota is org-wide; Release assets bypass it]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]

%% ai-graph-end %%