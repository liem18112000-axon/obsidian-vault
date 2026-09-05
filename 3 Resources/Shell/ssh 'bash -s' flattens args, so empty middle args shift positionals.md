---
title: "ssh 'bash -s' flattens args, so empty middle args shift positionals"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25 (leo-customer360 deploy)"
tags: [ssh, bash, deployment, gotcha, pipefail]
---

# ssh 'bash -s' flattens args, so empty middle args shift positionals

Passing many positional args to a remote heredoc via `ssh HOST '\''bash -s'\'' "$a" "$b" "$c" <<'\''REMOTE'\''` is fragile: ssh does NOT preserve argv. It **joins all the command words into one space-separated string** that the remote login shell then re-splits. So a quoted **empty** argument in the middle (e.g. `"$IMAGE"` on a local build, or an absent token) contributes only whitespace, collapses on the remote side, and **shifts every later positional arg left by one** — silently corrupting $N and beyond.

Symptom seen: a base64 blob (OTEL env) passed at $16 arrived as a different value and `base64 -d` produced 'invalid utf8 bytes', which broke a downstream `docker run --env-file`. Two sibling deploy scripts had the same latent bug; it only fired once an earlier arg went empty (BUILD_LOCAL=1 → empty IMAGE + empty GHCR token).

**Fix (the robust, proven pattern):** put ALL params into ONE base64 blob of `KEY=VALUE` lines and pass that as the single ssh arg; on the box `base64 -d` it to a temp file and `set -a; . tmp; set +a`. One non-empty arg → no flattening, no positional drift, and values with spaces/specials survive. Base64 the individual secrets inside the blob. (Values must be newline-free; `KEY=VALUE` lines with '\''='\'' inside the value are fine — the shell splits on the first '\''='\''.)

Corollary gotcha in the same code: `X="$(tr -dc A-Za-z0-9 </dev/urandom | head -c 32)"` aborts under `set -euo pipefail` because head closes the pipe → tr dies SIGPIPE(141) → pipefail propagates → set -e exits. Append `|| true` inside the substitution.

## Related

- [[Loopback-bind a bridge container that must reach a host-network service]]
