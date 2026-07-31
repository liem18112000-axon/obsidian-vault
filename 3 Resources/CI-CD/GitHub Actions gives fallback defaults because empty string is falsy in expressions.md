---
title: "GitHub Actions || gives fallback defaults because empty string is falsy in expressions"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04 fb-info-project sign-license.yml"
tags: [github-actions, ci, expressions, secrets]
---

# GitHub Actions || gives fallback defaults because empty string is falsy in expressions

In GitHub Actions expressions, `${{ A || B }}` returns A when A is truthy, otherwise B. An unset/empty secret or input expands to the empty string, which is FALSY, so `||` is the idiomatic way to provide a fallback or default:

```yaml
LICENSE_PRIVKEY: ${{ secrets.LICENSE_PRIVKEY || secrets.LICENSE_PUBKEY }}
timeout:         ${{ inputs.timeout || 300 }}
```

Referencing a secret/input that does not exist is NOT an error - it silently becomes "" - which is exactly why `||` works for it. Practical use: tolerate a legacy/mis-named secret while keeping the correct primary name first in the chain, or supply an input default without a separate `default:` block. Falsy values in GA expressions: `false`, `0`, `""`, `null` - so `inputs.count || 5` also replaces a literal 0, watch for that.

Related: [[GitHub Actions secret is not set usually means a name mismatch - verify with gh secret list]], [[Pass a value between GitHub Actions steps via GITHUB_OUTPUT and steps.id.outputs]]
