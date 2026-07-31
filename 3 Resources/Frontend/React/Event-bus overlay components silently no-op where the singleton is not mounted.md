---
title: "Event-bus overlay components silently no-op where the singleton is not mounted"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03, vinnstack Lightbox fix"
tags: [react, events, overlay, vinnstack]
---

# Event-bus overlay components silently no-op where the singleton is not mounted

The "dispatch a CustomEvent, one mounted singleton renders it" overlay pattern (openLightbox() -> window event -> <Lightbox/> listener) has a silent failure mode: any trigger rendered in a view that does NOT mount the singleton dispatches into the void - no error, no overlay, the button just "does nothing". In vinnstack, <Lightbox/> lived inside Chat, so diagram-enlarge worked in chat and silently failed in the Interrogation Room.

Rule: an event-bus overlay singleton belongs at the ROOT layout (app/layout.tsx body), mounted exactly once app-wide - never inside the feature that first needed it. Two mounts are also a bug (both react to the event -> stacked overlays), so move, don't copy.

Symptom to recognize: "clicking X does nothing" for a control that demonstrably works elsewhere in the app.
