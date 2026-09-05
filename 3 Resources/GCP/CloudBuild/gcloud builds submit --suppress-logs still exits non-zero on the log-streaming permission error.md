---
title: "gcloud builds submit --suppress-logs still exits non-zero on the log-streaming permission error"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03"
tags: [gcp, cloud-build, gcloud, ci-cd, gotcha]
---

# gcloud builds submit --suppress-logs still exits non-zero on the log-streaming permission error

The `gcloud builds submit` CLI returns a NON-ZERO exit code when the calling identity lacks permission to stream/read the build logs (`ERROR: ... This tool can only stream logs if you are Viewer/Owner of the project`), even though the build was submitted fine and completes SUCCESSFULLY server-side. The `--suppress-logs` flag does NOT prevent this — it stops printing logs but gcloud still polls build status through the logs and errors out.

Why it bites: in a deploy script running under `set -e`, this false failure kills the script AFTER the image is built + pushed but BEFORE later steps (e.g. a final `terraform apply -var=image=<tag>` that re-points the service), leaving a half-deployed state where infra is updated but the service still runs the old image.

Recovery: verify the build actually succeeded — `gcloud builds describe <build-id> --format='value(status)'` should print SUCCESS and the tag should be in the registry — then run the remaining deploy step manually.

Real fixes: grant the identity a role that can read build logs (Cloud Build Viewer / project Viewer), OR submit with `--async` so the CLI doesn't wait on logs at all, OR route build logs to your own bucket / use a dedicated build service account.

## Related

- [[Cloud Run's managed /cloudsql socket does not reach sidecar containers]]
