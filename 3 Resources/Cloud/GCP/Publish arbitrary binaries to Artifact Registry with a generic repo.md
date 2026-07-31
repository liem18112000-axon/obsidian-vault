---
ai_hash: d7b8ad44c6805170
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: vinnstack cloudbuild.yaml setup, 2026-07-07
status: seedling
tags:
- gcp
- artifact-registry
- cloud-build
- generic-repo
title: Publish arbitrary binaries to Artifact Registry with a generic repo
type: howto
---

# Publish arbitrary binaries to Artifact Registry with a generic repo

To publish an arbitrary binary (not a container image) from Cloud Build to Artifact Registry, create a repository with format `GENERIC` and push to it with:

```
gcloud artifacts generic upload   --project=<project> --location=<region> --repository=<repo>   --package=<name> --version=<version> --source=<file>
```

No Docker packaging or `docker push` involved — it'\''s a direct file upload API, distinct from the container-image and language-package (npm/maven/etc.) repo formats Artifact Registry also supports.

Cloud Build'\''s built-in `$SHORT_SHA` substitution (available automatically on any build triggered from a connected repo, no config needed) is a convenient `--version` value, since it ties every published artifact back to the exact commit that produced it. Generic repos have no built-in "latest" tag concept, but the convention of re-uploading the same file under a literal `--version=latest` gives a stable, overwritable pointer alongside the immutable SHA-versioned ones.

## Related
[[Cross-building Electron Windows exe on Linux needs wine]]
[[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]

%% ai-graph-start %%

**Related notes:**
- [[Publish a single file to GCS from Cloud Build with gcloud storage cp]]
- [[Vinnstack publishes its exe to a GCS bucket, not Artifact Registry]]
- [[Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[Wire electron-updater to a public GCS bucket via the generic provider]]

%% ai-graph-end %%