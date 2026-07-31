---
title: "Cloud Run can only pull images from Artifact Registry or GCR, not GHCR"
created: 2026-06-15
type: lesson
status: seedling
source: "session 2026-06-15, accesstrade_integration cloud run deploy"
tags: [gcp, cloud-run, artifact-registry, ghcr, docker, ci-cd]
---

# Cloud Run can only pull images from Artifact Registry or GCR, not GHCR

**Google Cloud Run can only pull container images from Artifact Registry (`*-docker.pkg.dev`) or the legacy Container Registry (`gcr.io`) in the SAME or an accessible GCP project — NOT from arbitrary registries like GitHub Container Registry (ghcr.io) or Docker Hub private repos.** So an image your CI pushed to GHCR cannot be deployed to Cloud Run directly.

Consequence for design: if you build/push to GHCR for general distribution but also want Cloud Run, you must ALSO push the image to an Artifact Registry repo (and grant the Cloud Run runtime SA's Artifact Registry reader — same-project pulls are automatic via the default compute/agent perms, but a cross-project pull needs `roles/artifactregistry.reader` for the Cloud Run Service Agent). The deploy pipeline then references the AR image, e.g. `<region>-docker.pkg.dev/<project>/<repo>/<name>:<tag>`.

In this project (accesstrade): docker-publish.yml keeps pushing to GHCR for convenience, and a separate deploy-web workflow pushes the same web image to an Artifact Registry repo (`google_artifact_registry_repository`) that the Cloud Run service pulls from. The CI authenticates Docker to AR with `gcloud auth configure-docker <region>-docker.pkg.dev`.

Related: pushing to AR from GitHub Actions needs the deploy SA to have `roles/artifactregistry.writer` (and the repo must exist). Relates to [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]] and [[GCP auth ambient ADC in GCP-hosted runners vs explicit creds in external CI]].
