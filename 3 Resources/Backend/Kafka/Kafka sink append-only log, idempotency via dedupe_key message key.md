---
ai_hash: 7694677c096b1395
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26 feat/appsflyer-pull-kafka-sink
status: seedling
tags:
- kafka
- idempotency
- cdp
- connector
- leo-cdp
- python
title: 'Kafka sink: append-only log, idempotency via dedupe_key message key'
type: lesson
---

# Kafka sink: append-only log, idempotency via dedupe_key message key

When Kafka is the CDP ingestion sink (connector publishes events; Leo CDP consumes the topic), a few patterns make the sink class clean and correct:

**A topic is an append-only log — there is no 'replace a partition'.** So a sink whose Protocol has both `write_partition` (batch/Pull) and `append_partition` (streaming/Push) implements BOTH as the same publish — the batch-vs-stream distinction (replace vs append) that matters for files/HTTP is meaningless for a log.

**Idempotency is delegated downstream via the message KEY.** Use each event's `dedupe_key` (content hash) as the Kafka message key. A log-compacted topic (or an upserting consumer) then collapses re-published duplicates — AppsFlyer push retries, or a re-run pull day — without the producer tracking state. This is how you keep at-least-once producing safe.

**Make the client optional + the sink testable:** import the Kafka client lazily inside `__init__` (so importing the module needs no `confluent-kafka` install) and accept an injectable `producer` (so unit tests pass a fake with `produce()`/`flush()` and need no broker). Ship the client as an optional extra (`pip install '.[kafka]'`).

**Delivery errors:** confluent_kafka's `produce()` is async; pass an `on_delivery(err, msg)` callback that records errors, then `flush()` (blocks until delivered/failed), then raise if any failed — turning fire-and-forget into a synchronous raise-on-failure like an HTTP sink.

For the Leo CDP AppsFlyer connector this let BOTH the push receiver and the pull pipeline/DAG share one KafkaSink. See [[A webhook receiver deploys as an always-on service, not a scheduled job]] and [[AppsFlyer Push layer appends per-event while Pull replaces the day]].

## Related

- [[3 Resources/Infra/Deployment/A webhook receiver deploys as an always-on service, not a scheduled job]]
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]

%% ai-graph-start %%

**Related notes:**
- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[Composite fan-out sink materialize once, best-effort, idempotent retries]]
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]

%% ai-graph-end %%