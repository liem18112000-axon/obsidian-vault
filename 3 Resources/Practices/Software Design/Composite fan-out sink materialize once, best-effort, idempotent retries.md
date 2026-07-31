---
title: "Composite fan-out sink: materialize once, best-effort, idempotent retries"
created: 2026-06-27
type: lesson
status: seedling
source: "session 2026-06-27 feat/appsflyer-pull-kafka-sink; MultiSink"
tags: [design-pattern, sinks, idempotency, fan-out, connector, leo-cdp]
---

# Composite fan-out sink: materialize once, best-effort, idempotent retries

To write the same data to several destinations at once (e.g. publish to Kafka AND POST to an HTTP endpoint), wrap them in a **composite/fan-out sink** that implements the same interface and forwards each call to every child. Three decisions make it correct:

**1. Materialize the input once.** Pipelines often pass a *generator* of events (so a single sink can stream without buffering). A fan-out sink MUST `list()` the generator before handing it to each child — otherwise the first child drains it and the rest see nothing. The cost: the partition is buffered in memory; you trade single-sink streaming for fan-out. Document it.

**2. Best-effort, not fail-fast.** Attempt EVERY child even if an earlier one raises; collect errors and re-raise the first afterwards. This stops one broken destination from starving a healthy one. Safe to retry the whole write because of #3.

**3. Idempotency makes partial failure harmless.** If sink A succeeds and sink B fails, the caller treats it as a retryable failure and replays the whole write — sink A then gets a duplicate. As long as each event carries a stable dedupe_key (content hash) and the destinations dedupe/upsert/compact on it, the replay to A is a no-op. Without per-event idempotency, fan-out + retry = double-writes.

CLI ergonomics: let `--sink` take a comma list (`kafka,cdp`); a single value returns that one sink, multiple wrap in the composite. Harden the per-kind builder to reject unknown kinds explicitly (free-form input exposes any 'unknown falls through to default' bug).

Used in the Leo CDP AppsFlyer connector (MultiSink) so both the push receiver and the pull pipeline/DAG can write to the endpoint and Kafka together. See [[3 Resources/Backend/Kafka/Kafka sink append-only log, idempotency via dedupe_key message key]].

## Related

- [[3 Resources/Backend/Kafka/Kafka sink append-only log, idempotency via dedupe_key message key]]
