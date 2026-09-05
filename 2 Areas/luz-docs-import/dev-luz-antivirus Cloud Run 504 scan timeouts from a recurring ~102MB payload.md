---
title: "dev-luz-antivirus Cloud Run 504 scan timeouts from a recurring ~102MB payload"
created: 2026-08-11
type: observation
status: seedling
source: "session 2026-08-11 AV scan-timeout check"
tags: [luz-antivirus, cloud-run, clamav, timeout, scancenter, dev]
---

# dev-luz-antivirus Cloud Run 504 scan timeouts from a recurring ~102MB payload

Observed 2026-08-11 on dev (`klara-nonprod` / `europe-west6`). The AV scanner **`dev-luz-antivirus` is a Cloud Run service** (not a GKE workload); in-cluster callers reach it via a `luz-antivirus:8080` Service/forwarder that fronts the Cloud Run URL.

**Symptom:** ~42 ERROR request-logs in 12h, dominated by **HTTP 504 capped at ~300 s** on `POST /luz_antivirus/api/scanner`.

Two classes:
- **Recurring ~102 MB payload** (`requestSize=102473382`, byte-identical) fails **~every 33 min**, always ~301 s → 504. The fixed size + regular cadence = a stuck **retry loop** re-submitting the same blob; ClamAV can't finish it inside the deadline.
- **Saturation burst** (~02:59–04:24): even **2 KB–167 KB** payloads timed out at 300 s. A few-KB file normally scans in ~30 ms, so the instance was jammed — the 102 MB scans monopolise the `containerConcurrency=10` slots. Also a few 503s (276 s / 375 s) = overload.

**Key config gotcha:** Cloud Run `timeoutSeconds=3600` (1 h), yet every 504 caps at **~300 s** → the real scan deadline is imposed **upstream of Cloud Run** (the in-cluster forwarder / GFE for the H2C path), *not* the container. So tuning only the Cloud Run timeout won't change the 300 s ceiling. Instance config: `containerConcurrency=10`, minScale=1, maxScale=58, startup-cpu-boost on.

**Fix direction:** (1) find & stop the ~102 MB retry source — it is NOT luz-docs-import (which only scans small `*.metadata.json` sidecars in ~30–100 ms); likely scancenter / luz-docs scanning a document binary. (2) Align caller read-timeout + proxy timeout + a ClamAV `StreamMaxLength`/max-scan-size so oversized files are **rejected fast** instead of hanging 300 s and starving concurrency. (3) luz-docs-import's new **30 s AV client timeout** (fail-fast → classify failed) is the correct pattern the 300 s-hanging callers lack.

Cross-check: no AV errors during the 06:11–06:24 Gap-3 import test — those scans were all fast 200s.

## Related

- [[luz-docs-import ZIP import timing: fresh 100-doc ~40s vs deduped sub-second]]
