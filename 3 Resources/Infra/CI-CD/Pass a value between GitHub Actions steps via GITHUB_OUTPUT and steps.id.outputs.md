---
ai_hash: a1371cfe29fb1afc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project sign-license.yml
status: seedling
tags:
- github-actions
- ci
- step-outputs
title: Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.<id>.outputs
type: howto
---

# Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.<id>.outputs

To hand a computed value from one GitHub Actions step to a later step in the same job: give the producing step an `id`, append `name=value` to the `$GITHUB_OUTPUT` file, then read it as `${{ steps.<id>.outputs.<name> }}`.

```yaml
- name: Compute
  id: verify
  run: echo "license_id=key-abc123" >> "$GITHUB_OUTPUT"
- name: Use
  env:
    LIC_ID: ${{ steps.verify.outputs.license_id }}
  run: ...
```

From inside an inline Python heredoc it is just a file append:
```python
with open(os.environ["GITHUB_OUTPUT"], "a") as f:
    f.write(f"license_id={license_id}\n")
```
This is the modern replacement for the deprecated `::set-output::` workflow command. Values persist only within the job; cross-job needs `jobs.<id>.outputs` + `needs`.

Related: [[Deliver a CI-minted credential via a masked short-retention artifact, not the run log]]

%% ai-graph-start %%

**Related notes:**
- [[Deliver a CI-minted credential via a masked short-retention artifact, not the run log]]
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]
- [[Set up GitHub Actions to GCP via Workload Identity Federation]]

%% ai-graph-end %%