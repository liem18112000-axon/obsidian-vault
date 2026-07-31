---
title: "Verify test files still exist on disk before trusting prior green test runs"
created: 2026-07-09
type: lesson
status: seedling
source: "luz_docs estimatedcount session, 2026-07-09 — two test files vanished untracked, no error signal, only surefire's zero-tests-matched message"
tags: [testing, debugging, gotcha, maven]
---

# Verify test files still exist on disk before trusting prior green test runs

In a session where background automation (linters, autonomous fix-agents, hooks) can modify or delete files outside my own tool calls, a previously-passing test run is NOT proof the test files still exist right now. I hit this directly: two test files I had authored and verified passing (14/14, exit 0) minutes earlier had silently vanished from disk by the time I re-ran the exact same test command -- no error, no warning, git showed nothing (they were untracked, so a deletion of an untracked file leaves zero trace), and no "file was modified" notice had been surfaced for that specific change the way it had been for other automated edits earlier in the session.

The symptom was subtle: `mvn test -Dtest="X*Test"` failed with "No tests matching pattern... were executed", NOT a compile error and NOT an obviously-test-related error message, plus the maven log casually said "Nothing to compile - all classes are up to date" for both compile and testCompile -- easy to misread as "everything is fine, tests just did not match the glob" rather than "the source files are gone."

Lesson: when a test run that previously passed suddenly reports zero matching tests, do not assume a glob/pattern typo -- verify with `ls`/`find` that the source files still physically exist before debugging the test command itself. In an environment with background file-modifying automation, "I verified this compiles and passes" is a point-in-time fact, not a durable guarantee -- re-verify before reporting final status if meaningful time/turns have passed since the last check.
