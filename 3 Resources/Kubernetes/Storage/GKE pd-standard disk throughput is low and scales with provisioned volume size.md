---
title: "GKE pd-standard disk throughput is low and scales with provisioned volume size"
created: 2026-08-12
type: concept
status: seedling
source: "luz_docs_import large-ZIP latency investigation 2026-08-12"
tags: [gke, gcp, pd-standard, storage, performance, gotcha]
---

# GKE pd-standard disk throughput is low and scales with provisioned volume size

On GCE/GKE a `pd-standard` disk (Kubernetes `storageClassName: standard`) has **sequential throughput proportional to its provisioned size**, and the per-GB rate is low (order of tens of MB/s even at hundreds of GiB). It is HDD-class network block storage, not SSD.

**Consequence:** using a `pd-standard` PVC as upload/scratch space makes large-file disk I/O a real latency bottleneck — e.g. two full-size writes + a read of a ~1 GB file can take ~a minute of wall-clock, serialized. Counter-intuitively a *small* `pd-standard` volume is slower, because throughput scales with size.

**Levers:** provision a larger volume (buys throughput), or switch to `pd-balanced`/`pd-ssd` for scratch that sees big sequential I/O. But first reduce the number of full-size passes over the disk — faster disk is a mitigation, removing redundant I/O is the real fix.

Related: [[RESTEasy MultipartFormDataInput buffers the whole upload to tmp before the app reads it]], [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]].

## Related

- [[RESTEasy MultipartFormDataInput buffers the whole upload to tmp before the app reads it]]
- [[luz_docs_import upload-zip is slow for large files due to a synchronous double-write]]
