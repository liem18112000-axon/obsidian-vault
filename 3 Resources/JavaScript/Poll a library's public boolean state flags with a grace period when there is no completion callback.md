---
title: "Poll a library's public boolean state flags with a grace period when there is no completion callback"
created: 2026-07-11
type: technique
status: seedling
source: "virtual-avatar session 2026-07-11, static/app.js waitForSpeechEnd"
tags: [javascript, async, polling, talkinghead, gotcha]
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
