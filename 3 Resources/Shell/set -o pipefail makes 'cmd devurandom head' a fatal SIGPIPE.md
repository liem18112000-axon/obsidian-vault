---
title: "set -o pipefail makes 'cmd </dev/urandom | head' a fatal SIGPIPE"
created: 2026-08-22
type: lesson
status: seedling
source: "leo-customer360 deployments/monitoring, session 2026-08-22"
tags: [bash, pipefail, sigpipe, set-e, gotcha, shell]
---

# set -o pipefail makes 'cmd </dev/urandom | head' a fatal SIGPIPE

In a bash script with **`set -euo pipefail`**, generating a random string with a pipe that reads from an INFINITE source and closes early is **fatal**:

```bash
PW="$(LC_ALL=C tr -dc 'A-Za-z0-9' </dev/urandom | head -c 24)"   # exits the whole script, code 141
```

**Why:** `head -c 24` reads 24 bytes then closes the pipe. `tr`, still reading the endless /dev/urandom, gets **SIGPIPE** and dies with exit 141 (128+13). `pipefail` propagates that 141 as the pipeline's status, and `set -e` then kills the script — SILENTLY (set -e prints nothing). Symptom: script exits 141 with no error message, dying at that assignment.

**Key nuance:** this is a STANDALONE assignment `PW=$(...)`, on which set -e DOES act. (A `local PW=$(...)` declaration would NOT trigger set -e, because `local` is the command whose 0 status wins — a classic reason the same line behaves differently inside vs outside a function.)

**Why it's easy to miss:** the exact same one-liner works fine when typed interactively (no set -e / pipefail) — e.g. copied from a README's manual setup step into a hardened script. It also 'works' if the source is FINITE (e.g. `head -c 200 /dev/urandom | tr ... | head -c 24`): tr finishes writing its bounded output into the 64KB pipe buffer and exits 0 before head closes, so no SIGPIPE.

**Fix:** use a single finite-output generator, no early-closing head:
```bash
PW="$(openssl rand -base64 24 | LC_ALL=C tr -dc 'A-Za-z0-9')"   # both commands finite -> exit 0
```

**General rule:** under pipefail, never pipe an unbounded producer into a consumer that closes early (`head`, `grep -m`, `sed q`). Either bound the producer or use a self-contained generator (openssl, $RANDOM).

Source: leo-customer360 deployments/monitoring/deploy-monitoring.sh — pgAdmin password auto-gen killed the deploy with a silent exit 141 (2026-08). Found via `bash -x`, which prints each command before running so the last traced line is the culprit even when set -e is silent.
