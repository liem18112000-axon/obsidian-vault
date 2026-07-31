---
title: "Google Cloud TTS timepointing (enable_time_pointing) requires the v1beta1 client"
created: 2026-07-10
type: lesson
status: evergreen
source: "virtual-avatar MVP implementation, 2026-07-10"
tags: [gcp, text-to-speech, gotcha, python]
---

# Google Cloud TTS timepointing (enable_time_pointing) requires the v1beta1 client

Google Cloud Text-to-Speech's word-timing feature — `enable_time_pointing` on `SynthesizeSpeechRequest` plus its `TimepointType.SSML_MARK` enum and the response's `timepoints` field (each with `mark_name`/`time_seconds`) — only exists on the **v1beta1** client (`google.cloud.texttospeech_v1beta1`), not the stable v1 client (`google.cloud.texttospeech`). Importing the stable module and referencing `texttospeech.SynthesizeSpeechRequest.TimepointType` raises `AttributeError: type object 'SynthesizeSpeechRequest' has no attribute 'TimepointType'` — the field is silently absent from that proto message entirely, not just deprecated.

Confirmed directly against the installed `google-cloud-texttospeech==2.37.0` package by inspecting `texttospeech_v1beta1.SynthesizeSpeechRequest` and `texttospeech_v1beta1.SynthesizeSpeechResponse` source — both include `enable_time_pointing`/`TimepointType` and `timepoints`/`Timepoint` respectively; the equivalent stable-v1 classes do not.

Fix: `from google.cloud import texttospeech_v1beta1 as texttospeech` instead of `from google.cloud import texttospeech`. Everything else (voice selection, audio config, SSML input, client class name) is identical between the two, so this is a drop-in import swap, not a rewrite.

Why this matters beyond this one bug: web research/documentation summaries (including a dedicated research pass right before writing this code) described the timepointing feature generically as being on "SynthesizeSpeechRequest" without flagging the v1-vs-v1beta1 split — the only way this surfaced was by actually installing the package and constructing the real objects. General lesson: for GCP client library fields that sound like they should be basic/stable, verify against the actual installed package rather than trusting a docs summary, especially for anything called out as introduced for a specific "beta" feature.

## Related
- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[Virtual avatar presenter project design plan]]

## Related

- [[met4citizen TalkingHead is a free browser-native 3D avatar library]]
- [[Virtual avatar presenter project design plan]]
