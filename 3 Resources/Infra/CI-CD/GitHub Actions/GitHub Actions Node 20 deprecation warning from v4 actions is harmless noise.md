---
title: "GitHub Actions Node 20 deprecation warning from v4 actions is harmless noise"
created: 2026-06-21
type: lesson
status: seedling
source: "session 2026-06-21, fb-info-project"
tags: [github-actions, ci, gotcha, node]
---

# GitHub Actions Node 20 deprecation warning from v4 actions is harmless noise

GitHub Actions runners now default to **Node 24**, and steps that use older actions emit a deprecation notice:

> Node 20 is being deprecated. This workflow is running with Node 24 by default. If you need to temporarily use Node 20, set `ACTIONS_ALLOW_USE_UNSECURE_NODE_VERSION=true`.

This is **harmless noise on the consumer side** — it does not indicate anything wrong with your workflow. It fires because the *action itself* (not your code) bundles a Node 20 runtime in its `action.yml` (`using: node20`). Common culprits still on Node 20: `actions/upload-artifact@v4`, `actions/download-artifact@v4`, `dorny/test-reporter@v1`.

There is **nothing to fix in your workflow**. Pinning these to `@v4`/`@v1` is correct; the warning clears only when upstream ships a Node 24 release of the action. Do NOT set `ACTIONS_ALLOW_USE_UNSECURE_NODE_VERSION=true` as a 'fix' — that forces the old runtime, the opposite of what you want.

Likewise the `punycode module is deprecated` `DEP0040` warning from these actions is internal-to-the-action noise, not your concern.

Don't confuse this notice with a real failure on the same step — e.g. an artifact upload can print the Node notice AND fail for an unrelated reason (quota hit). Read past the warning to the actual `Error:` line.

## Related

- [[GitHub Actions artifact storage quota: use short retention-days on handoff artifacts]]
