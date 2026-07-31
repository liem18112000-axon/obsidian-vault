---
title: "node-postgres percent-decodes the connection-string password"
created: 2026-07-03
type: lesson
status: seedling
source: "Vinnstack GKE deploy 2026-07-03"
tags: [postgres, node-postgres, url-encoding, gotcha]
---

# node-postgres percent-decodes the connection-string password

When you build a `postgresql://user:password@host/db` URL and hand it to node-postgres (`new Pool({ connectionString })`), the driver **percent-decodes** the userinfo. So any reserved/special character in the password (`&`, `<`, `,`, `@`, `/`, `#`, spaces, …) must be **percent-encoded** in the URL — otherwise the string parses wrong (truncated password, misread host) and the connection silently fails or authenticates as the wrong credentials.

**Rule:** encode just the password segment with `encodeURIComponent(password)` before interpolating it into the URL. The driver reverses it back to the literal password at connect time. Example: password `&<XAMf,bA` → `%26%3CXAMf%2CbA` in the URL.

This applies equally when storing the URL in a Kubernetes Secret consumed as `DATABASE_URL` — encode before `kubectl create secret --from-literal=url=...`.

## Related

- [[GKE Immediate-binding StorageClass deadlocks a single-replica StatefulSet across zones]]
