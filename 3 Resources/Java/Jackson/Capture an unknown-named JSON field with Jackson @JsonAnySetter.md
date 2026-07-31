---
title: "Capture an unknown-named JSON field with Jackson @JsonAnySetter"
created: 2026-07-29
type: lesson
status: seedling
source: "session 2026-07-29 luz_online_payment LUZ-157476"
tags: [java, jackson, lombok, deserialization, gotcha]
---

# Capture an unknown-named JSON field with Jackson @JsonAnySetter

When a REST-client ObjectMapper is configured with `FAIL_ON_UNKNOWN_PROPERTIES = false`, any provider field the DTO does not declare is silently dropped during deserialization. If you need a field whose exact JSON key is not yet confirmed (e.g. an upstream vendor has not told you the name), add a `@JsonAnySetter` catch-all method plus a `Map<String,Object>` on the DTO to capture every unmodelled property, then resolve the value by probing a small list of candidate keys.

This buys two things at once: deserialization never fails, and you can discover/forward the real field the moment it arrives — no second code change needed if the name is already in your candidate list.

Gotcha with Lombok `@Data`/`@Builder`: exclude the capture map from generated accessors and equals/hashCode/toString (`@Getter(AccessLevel.NONE)`, `@Setter(AccessLevel.NONE)`, `@ToString.Exclude`, `@EqualsAndHashCode.Exclude`), mark it `@JsonIgnore` so it is not re-serialized, and use `@Builder.Default` with a `new HashMap<>()` initializer so builder/no-args paths still get a non-null map.

See [[LUZ-157476 decline-code flow: luz-online-payment forwards, luz_store maps]] for the concrete use.

## Related

- [[LUZ-157476 decline-code flow: luz-online-payment forwards]]
- [[luz_store maps]]
