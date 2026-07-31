---
title: "Test resume by pre-seeding a checkpoint, not by simulating an interrupt"
created: 2026-06-15
type: lesson
status: seedling
source: "fb-info-project pause/resume, 2026-06-15"
tags: [testing, resume, determinism, integration-test, checkpoint]
---

# Test resume by pre-seeding a checkpoint, not by simulating an interrupt

To test a resume/checkpoint feature deterministically, **don't simulate the interruption** — pre-seed a checkpoint file that represents the state an interrupted run *would* have left, then run the job and assert it resumes correctly. This sidesteps the flakiness of trying to kill a real run at a precise point (thread races, KeyboardInterrupt timing, asyncio task cancellation).

**Why:** forcing a real mid-run interrupt at a deterministic point is hard — concurrent workers, where exactly the signal lands, partial flushes. But the *only* thing the resume code consumes is the checkpoint file. So construct that file directly (via the real checkpoint class, so its fingerprint/format are valid), mutate it to the desired 'partway' state, then exercise the real resume path against it. The interrupt-time *write* path is covered separately by unit tests on the checkpoint class.

**Concrete (fb-info-project):** seeded a checkpoint with link 1 = done, link 2 = collected-with-2-profiles, cache = {one profile's fields}. Ran the real `service.batch`; asserted it skipped link 1's collection, reused link 2's collected commenters (no re-collect), fetched only the uncached profile, and cleared the checkpoint on completion. Fully deterministic, no threads-with-Ctrl-C.

**Split of coverage:** unit tests = does an interrupt persist the right state (write path); pre-seeded integration test = does that state resume correctly (read path). Together they cover the feature without a flaky end-to-end interrupt.

Related: [[A persisted dedup cache doubles as a resume log]], [[3 Resources/Practices/Software Design/Checkpoint files atomic tmp+rename write plus an input fingerprint]]

## Related

- [[A persisted dedup cache doubles as a resume log]]
- [[3 Resources/Practices/Software Design/Checkpoint files atomic tmp+rename write plus an input fingerprint]]
