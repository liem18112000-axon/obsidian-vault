---
title: "Publish arbitrary binaries to Artifact Registry with a generic repo"
created: 2026-07-07
type: howto
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [gcp, artifact-registry, cloud-build, generic-repo]
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
