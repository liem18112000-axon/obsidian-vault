---
ai_hash: c1bc926aedc5b64c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project sign-license.yml
status: seedling
tags:
- github-actions
- ci
- expressions
- secrets
title: GitHub Actions || gives fallback defaults because empty string is falsy in
  expressions
type: lesson
---

# GitHub Actions || gives fallback defaults because empty string is falsy in expressions

In GitHub Actions expressions, `${{ A || B }}` returns A when A is truthy, otherwise B. An unset/empty secret or input expands to the empty string, which is FALSY, so `||` is the idiomatic way to provide a fallback or default:

```yaml
LICENSE_PRIVKEY: ${{ secrets.LICENSE_PRIVKEY || secrets.LICENSE_PUBKEY }}
timeout:         ${{ inputs.timeout || 300 }}
```

Referencing a secret/input that does not exist is NOT an error - it silently becomes "" - which is exactly why `||` works for it. Practical use: tolerate a legacy/mis-named secret while keeping the correct primary name first in the chain, or supply an input default without a separate `default:` block. Falsy values in GA expressions: `false`, `0`, `""`, `null` - so `inputs.count || 5` also replaces a literal 0, watch for that.

Related: [[3 Resources/Infra/CI-CD/GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]], [[Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.id.outputs]]

%% ai-graph-start %%

**Related notes:**
- [[secrets context is not available in GitHub Actions if conditions]]
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]
- [[GitHub Actions masks a secret's VALUE everywhere - a plaintext field logging as means it equals a secret]]
- [[Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.id.outputs]]
- [[GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern]]

%% ai-graph-end %%