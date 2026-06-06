---
title: "Export build artifacts from a multi-stage Docker build via a scratch stage + buildx --output"
created: 2026-06-04
type: howto
status: seedling
source: "session 2026-06-04 leo-cdp-framework"
tags: [docker, buildx, ci, testing, gotcha]
---

# Export build artifacts from a multi-stage Docker build via a scratch stage + buildx --output

Multi-stage Docker builds can export a chosen artifact (e.g. JUnit XML) to the host without dumping a whole image filesystem. Add a final stage `FROM scratch AS test-export` that `COPY --from=<earlier-stage>` only the wanted files, then run:

```
docker buildx build --target test-export --output type=local,dest=./out .
```

Because the stage is built on `scratch`, `--output type=local` writes ONLY those files to ./out. This lets you run a test suite *inside* `docker build` (in a `FROM build AS test` stage) and surface the reports to CI for a JUnit reporter.

**Gotcha:** a test step running inside buildx cannot reach CI service containers (ArangoDB/Redis/Postgres started by the CI job), because the build runs in an isolated network. So integration tests that need a live DB will fail there. Make the in-build test command failure-tolerant (`gradle test ... || true`) so the XML is still produced, and report pass/fail from the exported XML rather than gating on the docker build exit code. Used in leo-cdp-framework core-leo-cdp/Dockerfile.

## Related

- [[GitHub Actions Docker build and publish]]
