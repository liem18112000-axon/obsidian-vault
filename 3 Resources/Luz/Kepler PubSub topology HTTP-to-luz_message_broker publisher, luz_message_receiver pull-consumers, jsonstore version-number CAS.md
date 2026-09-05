---
title: "Kepler Pub/Sub topology: HTTP-to-luz_message_broker publisher, luz_message_receiver pull-consumers, jsonstore version-number CAS"
created: 2026-08-10
type: reference
status: seedling
source: "session 2026-08-10 (Tier 4 infra investigation)"
tags: [luz, pubsub, messaging, luz-message-broker, jsonstore, infrastructure]
---

# Kepler Pub/Sub topology: HTTP-to-luz_message_broker publisher, luz_message_receiver pull-consumers, jsonstore version-number CAS

Kepler messaging topology (as of 2026-08): services do NOT embed a Pub/Sub publisher — they HTTP POST to the shared `luz_message_broker` service, which publishes to Google Pub/Sub. Broker endpoints: `POST /message/{tenant}/companies/{id}/push/{topic-id}?orderingKey=...` and no-tenant `/message/push/{topic-id}` (+ a v2 with attributes). `PubSubMessageService.sendMessage` supports ordering keys end-to-end (`setEnableMessageOrdering`, `PubsubMessage.setOrderingKey`, `resumePublish`). Config key for the broker host = `LUZ_MESSAGE_BROKER_HOST_PORT`.

Consumers use the shared external lib `ch.klara.luz.message.receiver:luz_message_receiver` (`BaseReceiver` + `@RegisterSupscription(subcriptionId=...)`, streaming-pull, ack/nack) — hosted in luz_docs_view_controller (the doc-upload corridor: `dev-doc-upload-received-topic` + 5 subscriptions) and independently in luz_enrichment (which also has a dead-letter consumer). luz_store and luz_audit also consume. The Cloud Run terraform template alternatively uses PUSH-to-HTTP subscriptions.

Topics/subscriptions/DLQ are declared imperatively in `luz_kubernetes/cluster/gcp/manual_script/*.sh` (`gcloud pubsub topics/subscriptions create --enable-message-ordering --enable-exactly-once-delivery --ack-deadline --message-retention-duration`; DLQ via `--dead-letter-topic --max-delivery-attempts` on a beta update) with env vars in `configuration/env-<env>/env.sh`; OR reusably via `luz_kubernetes/terraform/common-cloudrun-resources/service-template/pubsub.tf` (topic + subscription + retry_policy + dead_letter_policy + DLQ topic/sub + IAM).

luz-jsonstore ALREADY supports optimistic-concurrency: `PATCH /{collection}/{doc-id}?version-number=N` filters on `_id`+`_versionNumber` (compare-and-set, returns UpdateResult so matchedCount==0 = stale); and `PATCH /{collection}/one` = findAndModify with caller filter. It does NOT auto-increment `_versionNumber` — caller must include the increment. Related: [[A durable queue fixes report-write durability, not data-duplication — make the side effect idempotent]].

## Related

- [[A durable queue fixes report-write durability]]
- [[not data-duplication — make the side effect idempotent]]
