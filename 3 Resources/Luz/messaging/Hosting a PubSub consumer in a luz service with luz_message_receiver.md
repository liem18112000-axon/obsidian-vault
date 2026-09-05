---
title: "Hosting a Pub/Sub consumer in a luz service with luz_message_receiver"
created: 2026-08-10
aliases: ["luz_message_receiver BaseReceiver RegisterSupscription"]
type: howto
status: seedling
source: "LUZ-158230 session 2026-08-10"
tags: [luz, pubsub, messaging, wildfly, luz-message-receiver, howto]
---

# Hosting a Pub/Sub consumer in a luz service with luz_message_receiver

To host a Google Pub/Sub **pull** consumer in a luz WildFly service, use the `ch.klara.luz.message.receiver:luz_message_receiver` lib (closest template: luz_docs `migration/`). Steps:

1. **Extend** `ch.klara.luz.message.receiver.service.pubsub.receiver.BaseReceiver`; annotate `@ApplicationScoped @RegisterSupscription(subcriptionId = "<system-property KEY>")`.
2. **Override `handleMessage(String data, AckReplyConsumer consumer)`** — the base `receiveMessage` already does `message.getData().toStringUtf8()` and calls `handleMessage`. It does **not** auto-ack, so YOU call `consumer.ack()` / `consumer.nack()` (nack ⇒ redelivery).
3. **Override `getSubscriberBuilder()`** to return `Subscriber.newBuilder(ProjectSubscriptionName.of(projectId, subscriptionName), this)`. When it returns **non-null**, the lib uses your builder and injects credentials, and the `@RegisterSupscription` value is treated as a **system-property KEY** (its value = the real subscription name). If you return **null**, the lib instead treats the annotation value as the **literal** subscription name.
4. **No bootstrap code** — the lib's `@Startup @Singleton @PostConstruct PubSubListenerRegister` scans `Instance<BaseReceiver>` and registers each. The WAR just needs `beans.xml` `bean-discovery-mode="all"`.
5. **Config** (read via `System.getProperty`): `google.pubsub.key` = inline SA-key JSON; `google.pubsub.project` = project id (`SystemPropertyService.GOOGLE_PUBSUB_PROJECT_ID` exposes it). Set via `deployment/wildfly/config/system-properties.xml`.

**Publisher side:** POST the raw JSON to the message-broker `/message/{tenant}/companies/{company}/push/{topic-id}?ordering-key=`. The broker wraps it into a Pub/Sub envelope `{tenantId, companyId, message, orderingKey}` where `message` is the inner JSON **string** — so the consumer double-parses (envelope, then inner). An `ordering-key` (e.g. the entity id) makes the broker publish with message-ordering ⇒ last-writer-wins per key.

Surfaced on LUZ-158230 (import-job Tier 4 durable status queue) in `luz_docs_import`.

## Related

- [[luz-jsonstore optimistic-concurrency PATCH version-number is an equality CAS]]
- [[luz WildFly WARs expose only the MicroProfile Fault-Tolerance spec]]
- [[not SmallRye @ExponentialBackoff]]
