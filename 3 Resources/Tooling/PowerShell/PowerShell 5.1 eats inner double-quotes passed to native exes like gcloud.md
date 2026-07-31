---
title: "PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud"
created: 2026-06-12
type: gotcha
status: seedling
source: "session 2026-06-12"
tags: [powershell, windows, gcloud, quoting]
---

# PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud

Windows **PowerShell 5.1** mangles arguments containing embedded double-quotes when it builds the command line for a native executable (e.g. `gcloud`, `git`, `kubectl`). A single-quoted PowerShell string like `'field="value"'` reaches the exe as `field=value` — the inner `"` are **stripped**. For `gcloud logging read` this surfaces as a confusing error far from the real cause:

```
ERROR: (gcloud.logging.read) INVALID_ARGUMENT: Unparseable filter:
  syntax error at line 1, column 200, token ":"
```

The `:` is from an unquoted timestamp (`2026-06-12T03:23:45Z`) — the value was never quoted because PowerShell ate the quotes, not because the timestamp is wrong.

**Fix:** escape every inner double-quote as `\"` inside the PowerShell string so the literal quote survives to the exe:

```powershell
$q = 'resource.labels.namespace_name=\"dev\" AND labels.\"k8s-pod/batch_kubernetes_io/controller-uid\"=\"d5cb...\" AND timestamp>=\"2026-06-12T03:23:45Z\"'
gcloud logging read $q --project=klara-nonprod --format=value(textPayload)
```

This is the documented PS 5.1 native-arg-passing bug (fixed by `PSNativeCommandArgumentPassing` in PowerShell 7+). When a native command rejects a value that looks correctly quoted in your script, suspect the quotes were stripped.

Related: [[Expand a cloudlogging.app.goo.gl share link via WebFetch to recover the gcloud filter]]

## Related

- [[Expand a cloudlogging.app.goo.gl share link via WebFetch to recover the gcloud filter]]
