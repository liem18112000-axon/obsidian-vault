---
ai_hash: 031a671a3a62fbf2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: virtual-avatar session 2026-07-11, static/app.js waitForSpeechEnd
status: seedling
tags:
- javascript
- async
- polling
- talkinghead
- gotcha
title: Poll a library's public boolean state flags with a grace period when there
  is no completion callback
type: technique
---

# Poll a library's public boolean state flags with a grace period when there is no completion callback

When a JS library exposes state as plain public boolean instance properties (e.g. `isAudioPlaying`, `isSpeaking`) instead of a completion callback/event/promise for an async operation it kicked off internally, you can drive "wait until it finishes" externally by polling those flags — but naively polling immediately after starting the operation risks observing the pre-start gap (flags still false because the operation has not actually begun yet) and resolving instantly, before anything actually played.

Fix: add a short grace-period delay before the first poll, so the operation has had a chance to flip the flags to true first:

```js
function waitForCompletion() {
  return new Promise((resolve) => {
    setTimeout(function poll() {
      if (!lib.isBusyFlagA && !lib.isBusyFlagB) resolve();
      else setTimeout(poll, 150);
    }, 150); // grace period before the first check
  });
}
```

Used in the virtual-avatar project (`static/app.js`, `waitForSpeechEnd()`) to sequence an autonomous narration loop through the `TalkingHead` JS library, which has `isAudioPlaying`/`isSpeaking` as plain public properties but no `onSpeechEnd`-style callback on `speakAudio()`. Without the initial grace period, the loop would immediately advance to the next item before the current one had even started playing.

## Related

- [[Unguarded top-level await in a module script blocks every statement after it]]

%% ai-graph-start %%

**Related notes:**
- [[State machines must catch expected-failure operations or they get stuck forever]]
- [[Unguarded top-level await in a module script blocks every statement after it]]
- [[TalkingHead speakAudio never decodes a single ArrayBuffer before playback]]
- [[Hand-rolled RMS energy voice activity detection with self-calibrating noise floor]]

%% ai-graph-end %%