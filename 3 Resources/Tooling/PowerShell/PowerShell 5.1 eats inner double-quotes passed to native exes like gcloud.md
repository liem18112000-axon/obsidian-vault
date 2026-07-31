---
title: "PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud"
created: 2026-06-12
type: gotcha
status: seedling
source: "sessions 2026-06-12 (gcloud logging read) and 2026-07-03 (vinnstack git commit failure)"
tags: [powershell, windows, gcloud, git, quoting, gotcha]
---

# PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud

Windows **PowerShell 5.1** does not escape embedded double-quotes when it rebuilds the command line for a native executable (`gcloud`, `git`, `node`, `kubectl`). A PowerShell string `'field="value"'` reaches the exe as `field=value` — the inner `"` are stripped and the argument splits at the first inner quote. Fixed in PowerShell 7+ by `PSNativeCommandArgumentPassing`; there is no fix in 5.1, only workarounds.

Two symptoms, both far from the real cause:
- `gcloud logging read` → `INVALID_ARGUMENT: Unparseable filter: syntax error at line 1, column 200, token ":"` — the `:` comes from a now-unquoted timestamp (`2026-06-12T03:23:45Z`), not a bad timestamp.
- `git commit -m` with a multi-line message containing `"quoted words"` → git sees several broken args and fails with `pathspec '<word-after-quote>' did not match any file(s)`, and **no commit is created**. Recognize it by the pathspec naming a word from the middle of your message.

**Fixes:**
- Escape each inner quote as `\"` in the PowerShell string so the literal quote survives:
  ```powershell
  $q = 'resource.labels.namespace_name=\"dev\" AND timestamp>=\"2026-06-12T03:23:45Z\"'
  gcloud logging read $q --project=klara-nonprod --format=value(textPayload)
  ```
- Or avoid quotes entirely in the argument (single quotes render fine), use the `--%` stop-parsing token, or pass via file (`git commit -F msg.txt`).

When a native command rejects a value that looks correctly quoted in your script, suspect the quotes were stripped.

## Related

- [[Resolve Cloud Logging share links via redirect Location header]]
