---
tags: [vinnstack, nextjs, python, venv, provisioning, ux]
---

# In-app provisioning of a Python-venv tool with a pollable install state

Pattern for letting a Next.js app install a Python-CLI dependency (here: Graphify = the `graphifyy` PyPI package in `~/.agentic-os/graphify/venv`) from the UI, with a live progress screen:

- **Detect** installed = `existsSync(venv/Scripts/graphify.exe)`. Cheap; drives a status badge and an install gate.
- **State machine, not a request/response.** Keep a module-level `InstallState { phase: "idle"|"venv"|"pip"|"firewall"|"done"|"error", log: string[], error?, version? }`. `POST /install` kicks off the work in the background and returns immediately; `GET /install` returns the current state. The client polls GET every ~1.5s while phase is in-progress. This survives the long (minutes) pip step without holding a request open.
- **Stream child stdout+stderr into `state.log`** (split on newlines, cap the array) so the polling screen shows real progress, not just a spinner.
- **Idempotent triggers:** POST is a no-op if already installed or already provisioning.

**Ordering gotcha (security):** pip install NEEDS network, but the runtime is egress-firewalled. So the order must be: create venv → `pip install` (network open) → THEN add the Windows Firewall outbound-block rules on the venv `python.exe`/`graphify.exe`. Reversing it means pip can't reach PyPI. Firewall rules need admin — make them **best-effort** (log a warning, don't fail the install), since the scan is offline regardless.

**Editing gotcha:** the existing `graphifyRunner.ts` had 3 stray NUL bytes (UTF-8, not UTF-16), which made the exact-match Edit tool unsafe. Rather than edit it, I put the install logic in a NEW module that redefines the deterministic paths — private consts (ROOT, VENV_*) aren't exported, but they're just `path.join(os.homedir(), ".agentic-os", "graphify", …)`, so duplicating them is trivial and avoids touching the corrupted file.

Related: [[Long agentic API routes need the inner run timeout below the route maxDuration]].
