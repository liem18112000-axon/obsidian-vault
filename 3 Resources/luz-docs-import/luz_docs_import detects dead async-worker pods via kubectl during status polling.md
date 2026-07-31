---
title: "luz_docs_import detects dead async-worker pods via kubectl during status polling"
created: 2026-07-31
type: lesson
status: seedling
source: "graphify investigation 2026-07-31, commit df874966"
tags: [luz-docs-import, kubernetes, async, ejb, resilience, pattern]
---

# luz_docs_import detects dead async-worker pods via kubectl during status polling

Because `luz_docs_import` runs the heavy import as a fire-and-forget `@Asynchronous` EJB (`DocsImportAsyncService.importZipAndCleanFile`, `@TransactionAttribute(NEVER)`) pinned to one pod, a crashed worker can no longer update its own job. The **read path repairs this**: `DocsImportService.getImportJob()` (the `GET /import-jobs/{id}` poll) recomputes terminal state on the fly.

Mechanism — for a job still running (`status != DONE && != FAILED`), `PodUtil.isPodRunning(executor)` shells out to `kubectl get pod <name> | grep Running` (only when `KUBERNETES_SERVICE_HOST` is set; otherwise assumed alive):
- pod alive **and** `lastModifield` older than **1 hour** -> `FAILED` + `TIMEOUT`;
- pod **gone** -> `FAILED` + `INTERNAL_SERVICE_ERROR`.

The `executor` field is stamped at job creation from `System.getenv("HOSTNAME")` (the pod name) via `PodUtil.getRunningPodName()`.

**Gotchas / how to apply:**
- The repair is computed on each poll but **not persisted** (no `updateJob` in `getImportJob`), so timeout/crash jobs get no completion notification — notifications only fire from the async worker itself.
- `PodUtil` depends on `kubectl` being on the container PATH and RBAC-permitted for the pod SA — a fragile-but-pragmatic liveness probe worth flagging in ops reviews.
- `HOSTNAME` is the standard Kubernetes-injected pod name, not custom deploy config — no system.properties overlay needed.

Reusable pattern: **detect dead async workers from the reader, not the worker** — stamp the worker identity + last-heartbeat on the job, and let clients (or a sweeper) declare death. See repo report `docs/document-import-technical-path.md` (§6). Related: [[luz_docs_import ZIP uploads are not virus-scanned (AntiviusScanningService is never wired in)]]

## Related

- [[luz_docs_import ZIP uploads are not virus-scanned (AntiviusScanningService is never wired in)]]
