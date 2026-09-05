---
title: "Verify a GitHub Action's Node runtime and inputs via gh api on action.yml at a tag"
created: 2026-08-20
type: howto
status: seedling
source: "session 2026-08-20, leo-customer360 issue #6"
tags: [github-actions, gh-cli, ci, howto, node24]
---

# Verify a GitHub Action's Node runtime and inputs via gh api on action.yml at a tag

To confirm, without guessing, whether a GitHub Action version runs on Node 24 (vs deprecated Node 20) and whether the inputs you use still exist, read the action manifest at the exact tag via the API:

```bash
# Node runtime of a tag:
gh api "repos/OWNER/REPO/contents/action.yml?ref=TAG" -q .content | base64 -d | grep -iE "using:\s*.?node"
# -> using: node24   (good)   /   using: node20  (deprecated)

# Latest major to bump to:
gh api repos/OWNER/REPO/releases/latest -q .tag_name

# Enumerate the action inputs (verify yours survived a major bump):
gh api "repos/OWNER/REPO/contents/action.yml?ref=TAG" -q .content | base64 -d \
  | awk "/^inputs:/{f=1;next} /^[a-zA-Z]/{if(f)exit} f&&/^  [a-zA-Z_]/{gsub(\":\",\"\");print \$1}"
```

Notes: the Contents API returns base64 that must be `base64 -d`-decoded; try `action.yaml` if `action.yml` 404s. Pinning to a **major** tag (e.g. `@v7`) resolves to the latest release in that major, so checking the manifest at `@v7` reflects what the runner will use. This is how issue #6 bumps in `leo-customer360` were validated (all 9+ actions confirmed node24; send-mail v4→v18 and download-artifact v4→v8 confirmed to still carry the workflows inputs). Pair with [[An if-guarded GitHub Actions step emits no deprecation annotation until its condition is true]].

## Related

- [[An if-guarded GitHub Actions step emits no deprecation annotation until its condition is true]]
