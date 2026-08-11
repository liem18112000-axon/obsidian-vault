---
ai_hash: 8d9a204e070a2042
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-29
entities: []
source: session 2026-07-29 luz_online_payment LUZ-157476
status: seedling
tags:
- java
- jackson
- lombok
- deserialization
- gotcha
title: Capture an unknown-named JSON field with Jackson @JsonAnySetter
type: lesson
---

# Capture an unknown-named JSON field with Jackson @JsonAnySetter

When a REST-client ObjectMapper is configured with `FAIL_ON_UNKNOWN_PROPERTIES = false`, any provider field the DTO does not declare is silently dropped during deserialization. If you need a field whose exact JSON key is not yet confirmed (e.g. an upstream vendor has not told you the name), add a `@JsonAnySetter` catch-all method plus a `Map<String,Object>` on the DTO to capture every unmodelled property, then resolve the value by probing a small list of candidate keys.

This buys two things at once: deserialization never fails, and you can discover/forward the real field the moment it arrives — no second code change needed if the name is already in your candidate list.

Gotcha with Lombok `@Data`/`@Builder`: exclude the capture map from generated accessors and equals/hashCode/toString (`@Getter(AccessLevel.NONE)`, `@Setter(AccessLevel.NONE)`, `@ToString.Exclude`, `@EqualsAndHashCode.Exclude`), mark it `@JsonIgnore` so it is not re-serialized, and use `@Builder.Default` with a `new HashMap<>()` initializer so builder/no-args paths still get a non-null map.

See [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]] for the concrete use.

## Related

- [[1 Projects/luz_store/LUZ-157476/LUZ-157476 decline-code flow luz-online-payment forwards, luz_store maps]]

%% ai-graph-start %%

**Related notes:**
- [[luz_online_payment Payrexx webhook uses JSON-only Jackson mapper with FAIL_ON_UNKNOWN_PROPERTIES off]]
- [[Add response fields tolerant-reader-first when producer and consumer deploy separately]]
- [[DeclineCodes resolver misses nested metadata.decline_code]]
- [[KlaraPay DTOs are code-blind - lenient Jackson drops any Payrexx decline code]]
- [[TransactionStatus.from returns null on unknown Payrexx status causing silent NotNull 400]]

%% ai-graph-end %%