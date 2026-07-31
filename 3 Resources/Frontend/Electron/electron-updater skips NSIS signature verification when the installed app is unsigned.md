---
title: "electron-updater skips NSIS signature verification when the installed app is unsigned"
created: 2026-07-16
type: lesson
status: seedling
source: "Vinnstack session 2026-07-15 (research)"
tags: [electron-updater, electron, code-signing, nsis, auto-update, vinnstack]
---

# electron-updater skips NSIS signature verification when the installed app is unsigned

**Auto-update works for an UNSIGNED Windows Electron app.** On the NSIS update path, electron-updater only runs Authenticode signature verification when the *installed* app has a `publisherName` — which is populated only when the app was built with a code-signing certificate. For an unsigned app `publisherName` is null, so `NsisUpdater`'s `verifySignature` short-circuits with `if (publisherName == null) return null` (and also returns null if `app-update.yml` is missing / ENOENT). Returning null means 'verification passed/skipped', so the downloaded installer runs without a cert check → **the update installs**.

Corollary: the notorious `New version is not signed by the application owner` error can ONLY occur on the *signed* path (installed certs don't match the update's) — it can never happen to an unsigned app.

Integrity is not lost entirely: electron-updater still verifies the downloaded installer's **sha512** against `latest.yml`, so corrupted/truncated downloads are rejected. What's missing without signing is *publisher authenticity* — compensate with HTTPS delivery + a write-restricted (CI-only) update bucket. Signing is therefore RECOMMENDED, not REQUIRED, for auto-update; it's a UX/trust upgrade (removes Defender first-launch scan + SmartScreen), not a functional prerequisite.

Source: electron-updater `packages/electron-updater/src/NsisUpdater.ts`. Context: Vinnstack (unsigned NSIS, GCS-hosted, Electron 32).

## Related

- [[3 Resources/Frontend/Electron/Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]
- [[Electron]]
