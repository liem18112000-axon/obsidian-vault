---
title: "SSH flattens remote command args, so empty-string arguments collapse and shift positionals"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 UAT deploy, 2026-09-03"
tags: [ssh, bash, shell, deployment, gotcha]
---

# SSH flattens remote command args, so empty-string arguments collapse and shift positionals

When you run a command over SSH like `ssh host cmd a "$B" c`, OpenSSH does NOT preserve the argv boundaries — it **joins all the arguments into a single string** and hands that string to the remote login shell, which re-splits it on whitespace. Consequence: any **empty-string argument collapses** (vanishes), so every positional parameter after it shifts left by one. Quoting on the local side (`"$B"`) does not help — the local quotes are consumed locally; the remote shell only sees the flattened string.

Symptom: a remote `bash -s` reading `$1..$N` gets **mis-aligned values** when an earlier arg was empty. In one real case, deploying with `ssh host bash -s "$DB..." "$IMAGE" "$USER" "$TOKEN_B64" "$S3_ENDPOINT" ...` where `$IMAGE` and `$TOKEN_B64` were empty (build-from-source, no registry token) shifted the S3 values two slots: the region variable received the access key and the credential vars came out empty.

Robust fixes:
- **Pass one always-non-empty blob**: base64-encode the entire payload (e.g. the whole env file) into a SINGLE argument, and `base64 -d` it on the remote. Immune to collapsing.
- If you must pass several, **put every possibly-empty arg LAST** so a collapse shifts nothing important, and default them remotely (`${4:-}`).
- Never rely on an empty positional arg surviving an ssh hop.

Also: positional params past 9 in bash need braces — `${10}`, not `$10` (`$10` is `$1` + literal `0`).

## Related
[[Fail-open service config: render the instance config at container start from backend probes]]

## Related

- [[Fail-open service config: render the instance config at container start from backend probes]]
