---
title: "Perf import failures root-cause: luz-vault sealed/unready cascades jsonstore 503 to upload-zip 500"
created: 2026-08-24
type: observation
status: seedling
source: "session 2026-08-24"
tags: [luz-vault, luz-jsonstore, luz-docs-import, performance, root-cause, LUZ-158230, cascade-failure]
---

# Perf import failures root-cause: luz-vault sealed/unready cascades jsonstore 503 to upload-zip 500

On performance (2026-08-24), the k6 import load test failed 100% because **`luz-vault` is sealed / not-ready**, not because of anything in luz-docs-import. Both `luz-vault-*` containers report `ready=false` (since ~23 Aug), `/v1/sys/health?standbyok=true` returns **503**, and `luz-vault-unseal-0` has restarted 7x.

**Cascade:** Vault 503 → `luz-jsonstore` `document-import-jobs/add` (addOne) needs Vault transit crypto → throws `VaultException: ... status code 503` → jsonstore returns **HTTP 400** to import → import `DocsResponseExceptionMapper` maps 400 → `DocsException` → `UnexpectedExceptionMapper` → **HTTP 500** on `upload-zip`.

**Load-dependent disguise:** at **10 VUs** the failure shows through *fast* (~100ms, HTTP 500). At **100 VUs** the import worker pool saturates and requests queue to the 60s k6 timeout *before* reaching the Vault call, so it looked like a saturation/liveness death-spiral instead. Both are real, but Vault-down is the PRIMARY blocker; saturation is a high-load amplifier. See [[Liveness-probe death spiral: killing a thread-pool-saturated pod turns overload into a self-perpetuating outage]].

**Diagnostic technique that worked:** re-run at LOW concurrency to strip away saturation and expose the underlying correctness error; then trace one request thread top-to-bottom across services (import access log -> import REST-client filters -> jsonstore SEVERE -> vault /sys/health) to reach the bottom of the stack.

**Fix:** unseal/repair luz-vault on performance (investigate crash-looping `luz-vault-unseal-0`) — infra/Vault-owner action, likely outside app-team kubectl scope. Re-run only after `luz-vault-*` are 2/2 Ready and `/sys/health`=200. Full report: `docs/tests/perf-k6-loadtest-2026-08-24/root-cause-CORRECTION-vault.md`.

## Related

- [[luz-docs-import upload-zip endpoint is the ingestion saturation point under perf load]]
- [[Liveness-probe death spiral: killing a thread-pool-saturated pod turns overload into a self-perpetuating outage]]
## ✅ CONFIRMED — 10-VU re-run after Vault recovered = 100% pass

After `luz-vault-0/1` came back to **2/2 Ready** (`ready=true`), the identical 10-VU / 10-RPS / 1000-request case was re-run (build `fcdea5a3…`, pod `…-b6628`) and **passed completely**:

- **checks: 100.00% (7000/7000)** — all 7 assertions green: `upload-zip status is 200`, reached terminal in time, status/successfulFiles/skippedFiles/failedFiles/rejectedFiles all match expected.
- **http_req_failed: 0.00%** (0/3083); 1000/1000 iterations, 0 interrupted.
- **import_e2e_duration_ms**: avg 6409, med 6064, p90 12.1s, p95 15.1s, p99 18.4s, max 21.4s.
- **import_upload_duration_ms**: avg 85, med 37, p95 125, p99 1153, max 4888.
- Run wall-clock ~11m at 10 VUs (vus 3–10), data_sent 183 MB.

This closes the loop: the 100% failure in both prior runs was **entirely** the Vault outage. With Vault healthy, the import path is fully functional at 10 VUs. Next step is to ramp back toward 100 VUs to find the *real* capacity ceiling (which may then surface genuine import saturation / AV limits).
