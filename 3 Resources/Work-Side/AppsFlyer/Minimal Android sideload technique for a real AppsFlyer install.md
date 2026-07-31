---
ai_hash: 29ca71ac746db0d2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-13
entities: []
source: appsflyer-data-connector project, 2026-07-13
status: seedling
tags:
- appsflyer
- android
- adb
- mobile-tracking
title: Minimal Android sideload technique for a real AppsFlyer install
type: howto
---

# Minimal Android sideload technique for a real AppsFlyer install

To create one genuine AppsFlyer install for a given app (for testing attribution — see [[AppsFlyer only attributes events to installs recorded under the same app_id]]), the fastest path is a throwaway Android app rather than iOS:

1. New Android Studio project with `applicationId` set to the exact target AppsFlyer app_id.
2. Add the AppsFlyer Android SDK dependency, and in `Application.onCreate()` call `AppsFlyerLib.getInstance().init(devKey, null, this)` then `.start(this)`.
3. Build the debug APK and sideload it with `adb install app-debug.apk` on a real device — no Play Store publishing or review needed.
4. Launch the app once. The SDK's first-open call reaches AppsFlyer's servers and records a real install under that app_id, with a genuine `appsflyer_id` (and GAID, if Play Services is present).
5. Retrieve the AFID from the AppsFlyer dashboard, from `adb logcat` (the SDK logs it on start), or via `AppsFlyerLib.getInstance().getAppsFlyerUID(context)`.

Android beats iOS for this specific minimal-effort goal because iOS would require Xcode code signing and a provisioning profile to get the same result; Android sideloading skips all of that.

One caveat: this creates roughly one real install per device lifecycle — uninstalling/reinstalling the debug APK or clearing app data does not reliably create a second distinct install, since AppsFlyer dedupes installs per device within a cooldown window.

## Related
- [[AppsFlyer only attributes events to installs recorded under the same app_id]]

## Related

- [[AppsFlyer only attributes events to installs recorded under the same app_id]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer only attributes events to installs recorded under the same app_id]]
- [[How to get real AppsFlyer Pull API data with the synthetic generator]]
- [[AppsFlyer appsflyer_id is minted at install — fabricated IDs can't round-trip through Pull API]]
- [[AppsFlyer Push API is the inverse of the Pull API]]

%% ai-graph-end %%