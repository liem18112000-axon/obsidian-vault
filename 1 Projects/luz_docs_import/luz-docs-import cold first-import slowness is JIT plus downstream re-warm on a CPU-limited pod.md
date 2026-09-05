---
title: "luz-docs-import cold first-import slowness is JIT plus downstream re-warm on a CPU-limited pod"
created: 2026-08-13
type: observation
status: seedling
source: "docs/tests/import-run1-coldstart-root-cause.md 2026-08-13"
tags: [luz-docs-import, performance, cold-start, jit, LUZ-158230]
---

# luz-docs-import cold first-import slowness is JIT plus downstream re-warm on a CPU-limited pod

In `luz-docs-import`, the first (cold) ZIP-import run of each benchmark case was ~1.6–2.4× slower than the warm runs **even though documents/folders/document-import-jobs are truncated before every run**. Root cause (full evidence in `docs/tests/import-run1-coldstart-root-cause.md`): **warm-up of the hot per-document create path** — HotSpot **JIT** (CONFIRMED; owns the batch-size discriminator) **+ downstream service re-warming** (view-controller / jsonstore / antivirus, re-cooled over the ~2h idle *between* cases; CONFIRMED, dominant for the big run-1 spike) — amplified by the **CPU-limited 3-core Shenandoah pod** where JIT/GC threads contend with the 16 concurrent import workers.

**Fingerprint:** the `createDocument` round-trip (the dominant cost) has a **flat median (~600–660 ms) cold vs warm but a 2–4× fatter tail when cold** (p90 3457→1110 ms, 5k run1→run2). **Ruled out:** connection-pool/TLS warm-up — all four REST clients send `Connection: close` (`Constants.java:50-51`), plaintext HTTP, no pool to populate; thread-pool ramp — a fresh, fully-sized 16-thread executor per import. Steady-state ≈ **950–980 ms/doc (~14–15 docs/s)** regardless of batch size. Prod impact: cold-start only bites the **first import after a pod (re)start or long idle**.

Related: [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]], [[Truncating DB collections between benchmark runs resets data but not service warmth]], [[Small per-run batches warm the JIT gradually across runs; large batches warm within one run]], [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]].

## Related

- [[A latency penalty in the tail not the median points to JIT/GC warm-up under CPU contention]]
- [[Truncating DB collections between benchmark runs resets data but not service warmth]]
- [[Small per-run batches warm the JIT gradually across runs; large batches warm within one run]]
- [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]]
