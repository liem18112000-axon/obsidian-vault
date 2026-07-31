---
title: "Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.<id>.outputs"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04 fb-info-project sign-license.yml"
tags: [github-actions, ci, step-outputs]
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
