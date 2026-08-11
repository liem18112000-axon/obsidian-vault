---
ai_hash: 9c2006554c10a770
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
source: Vinnstack session 2026-07-15 (research)
status: seedling
tags:
- electron-updater
- electron
- code-signing
- nsis
- auto-update
- vinnstack
title: electron-updater skips NSIS signature verification when the installed app is
  unsigned
type: lesson
---

# electron-updater skips NSIS signature verification when the installed app is unsigned

**Auto-update works for an UNSIGNED Windows Electron app.** On the NSIS update path, electron-updater only runs Authenticode signature verification when the *installed* app has a `publisherName` — which is populated only when the app was built with a code-signing certificate. For an unsigned app `publisherName` is null, so `NsisUpdater`'s `verifySignature` short-circuits with `if (publisherName == null) return null` (and also returns null if `app-update.yml` is missing / ENOENT). Returning null means 'verification passed/skipped', so the downloaded installer runs without a cert check → **the update installs**.

Corollary: the notorious `New version is not signed by the application owner` error can ONLY occur on the *signed* path (installed certs don't match the update's) — it can never happen to an unsigned app.

Integrity is not lost entirely: electron-updater still verifies the downloaded installer's **sha512** against `latest.yml`, so corrupted/truncated downloads are rejected. What's missing without signing is *publisher authenticity* — compensate with HTTPS delivery + a write-restricted (CI-only) update bucket. Signing is therefore RECOMMENDED, not REQUIRED, for auto-update; it's a UX/trust upgrade (removes Defender first-launch scan + SmartScreen), not a functional prerequisite.

Source: electron-updater `packages/electron-updater/src/NsisUpdater.ts`. Context: Vinnstack (unsigned NSIS, GCS-hosted, Electron 32).

## Related

- [[3 Resources/Frontend/Electron/Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]
- [[Electron]]

%% ai-graph-start %%

**Related notes:**
- [[Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]
- [[Unsigned NSIS install under Defender ~2min of zero files is pre-scan, not a hang]]
- [[Wire electron-updater to a public GCS bucket via the generic provider]]
- [[Since June 2023 code-signing certs must be on HSM — CI needs a cloud signing service, not a .pfx]]
- [[Unsigned asarfalse Electron app ~30s first-launch delay is Defender scanning loose files]]

%% ai-graph-end %%