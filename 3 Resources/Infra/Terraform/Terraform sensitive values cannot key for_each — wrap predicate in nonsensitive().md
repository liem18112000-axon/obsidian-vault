---
ai_hash: 3bf279ce0b2d3f7e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-15
entities: []
tags:
- terraform
- gotcha
- secrets
title: Terraform sensitive values cannot key for_each — wrap predicate in nonsensitive()
---

# Terraform sensitive values cannot key for_each — wrap predicate in nonsensitive()

Terraform refuses to use a **sensitive value** — or anything derived from one — as a `for_each` key or `count`, because the value could leak as a resource instance address in plan output / state:

```
Error: Invalid for_each argument
Sensitive values, or values derived from sensitive values, cannot be used
as for_each arguments.
```

This bites when you want to conditionally create resources based on whether a sensitive variable was supplied — e.g. seed a `google_secret_manager_secret_version` only when `var.api_key != ""`. The boolean `var.api_key != ""` is *derived from* a sensitive var, so it poisons any `for_each`/`count`/conditional it feeds.

## Fix
Wrap just the **predicate** (not the secret itself) in `nonsensitive()`. Whether a value is empty is not sensitive, even though it's derived from a sensitive var:

```hcl
locals {
  versioned_secrets = toset(concat(
    ["always-a", "always-b"],
    nonsensitive(var.api_key != "") ? ["api-key"] : [],
  ))
}

resource "google_secret_manager_secret_version" "v" {
  for_each    = local.versioned_secrets        # keys are plain strings
  secret      = google_secret_manager_secret.s[each.key].id
  secret_data = local.values[each.key]          # the sensitive value stays sensitive
}
```

Key idea: keep the **map/set keys non-sensitive** (use names as keys, look the sensitive value up by key), and only `nonsensitive()` the small boolean/emptiness check — never the secret payload. Same pattern works for a `dynamic` block whose `for_each` is built with a sensitive-derived condition.

%% ai-graph-start %%

**Related notes:**
- [[secrets context is not available in GitHub Actions if conditions]]

%% ai-graph-end %%