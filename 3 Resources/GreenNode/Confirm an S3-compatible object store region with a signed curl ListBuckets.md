---
title: "Confirm an S3-compatible object store region with a signed curl ListBuckets"
created: 2026-08-03
type: howto
status: seedling
source: "session 2026-08-03"
tags: [s3, curl, sigv4, vstorage, region, technique]
---

# Confirm an S3-compatible object store region with a signed curl ListBuckets

To find which region an S3-compatible object store a given Access/Secret key belongs to (e.g. GreenNode vStorage), send a **signed ListBuckets** to each candidate endpoint and see which returns `200`:

```bash
curl -s -o /dev/null -w "%{http_code}" \
  --aws-sigv4 "aws:amz:<region>:s3" \
  --user "$ACCESS:$SECRET" \
  "https://<region>.vstorage.vngcloud.vn/"
```

- The correct region returns **200** with a `ListAllMyBucketsResult` (bucket names in `<Name>` tags).
- A mismatched region returns **403 `SignatureDoesNotMatch`** — S3 keys are region/project-scoped and the SigV4 signature encodes the region, so the same key only validates against its own region.

Needs `curl >= 7.75` (`--aws-sigv4`); no AWS SDK/CLI required. Never echo the secret — read it from an env/`.env` var and pass via the variable, and print only the HTTP code + `<Name>`/`<Code>` elements.

## Related
- [[vStorage has no Terraform resource so manage buckets via the AWS S3 provider]]
- [[VNG Cloud resource ID prefixes and the HCM zone_id label gotcha]]
