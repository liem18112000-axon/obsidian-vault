---
title: "Wire electron-updater to a public GCS bucket via the generic provider"
created: 2026-07-16
type: howto
status: seedling
source: "Vinnstack session 2026-07-16"
tags: [electron-updater, gcs, electron-builder, ci, auto-update, vinnstack]
---

# Wire electron-updater to a public GCS bucket via the generic provider

To auto-update an electron-builder app from a **public Google Cloud Storage** bucket (GCS is not a first-class electron-updater provider), use the **generic** provider pointed at the bucket's public HTTPS path:

```json
"publish": [{ "provider": "generic", "url": "https://storage.googleapis.com/<bucket>/latest/", "channel": "latest" }]
```

Key non-obvious points:
- **`electron-builder --win nsis --publish always` uploads NOTHING for the generic provider** — it has no uploader. `always` just makes electron-builder *emit* the update metadata (`latest.yml` + `.blockmap`) into `dist/`. So it needs no cloud credentials; your existing CI upload step (e.g. `gcloud storage cp`) does the actual upload. (Without `--publish`, `latest.yml`/`.blockmap` are never generated — issue electron-builder#1742.)
- **All three files must sit in ONE fixed folder** (`latest/`): the installer, `<installer>.blockmap`, and `latest.yml`. A per-commit/SHA folder can't be a feed (URL changes each build).
- **Upload `latest.yml` with `Cache-Control: no-cache`** (`gcloud storage cp ... --cache-control='no-cache, max-age=0'`) or clients keep seeing the old version for up to GCS's default 1h object-cache TTL. The exe/blockmap are content-hashed in the manifest, so they can stay cacheable.
- Bucket `latest/` must be **public-read** (uniform bucket access → one `allUsers` `roles/storage.objectViewer` IAM grant); keep WRITE restricted to CI only (integrity substitute when unsigned).
- Adding the `publish` block also auto-embeds `app-update.yml` into the app (electron-updater needs it); dev has none, so guard `checkForUpdates()` behind `!isDev`.

Context: Vinnstack, NSIS oneClick, Cloud Build → `gs://vinnstack-exe-release/latest/`.

## Related

- [[electron-updater skips NSIS signature verification when the installed app is unsigned]]
