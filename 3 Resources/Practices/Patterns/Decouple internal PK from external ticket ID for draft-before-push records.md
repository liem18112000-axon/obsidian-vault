---
title: "Decouple internal PK from external ticket ID for draft-before-push records"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session 2026-07-08"
tags: [architecture, data-modeling, jira, integration]
---

# Decouple internal PK from external ticket ID for draft-before-push records

When integrating with an external ticketing/issue system (Jira, Linear, GitHub Issues) but you want to let users draft/compose records before committing them externally, decouple your own primary key from the external system's ID rather than using the external ID as your PK.

Concretely: keep a stable internal identifier (assigned locally, e.g. sequential "S1", "S2", …) as the PK, and add a separate nullable `external_id`-style column (e.g. `jira_key`) that stays NULL until the record is actually pushed to the external system, then gets populated with the ID the external API returns. Also persist a local copy of any data the external system would otherwise be the sole source of (e.g. a `description` field), so downstream logic never has a hard dependency on the external record existing yet.

## Why

Using the external ID as your own PK from the start collapses two identities into one: "this record exists in my system" and "this record exists in the external system." That makes a "drafted locally, not yet pushed" state impossible to represent, and it usually leaks — other logic ends up fetching live data from the external API by that ID, so nothing works until the external ticket is created.

## Example

In the vinnstack Interrogation Room, a Jira Story's `key` used to always be its real Jira issue key — used as the DB primary key, used to fetch the Story's description live from Jira via REST, and used in every cross-reference. Splitting this into `key` (stable internal id, minted locally the moment a Story is decomposed from a PRD) and `jira_key` (nullable, populated only once the Story is actually pushed to Jira) let Stories exist as local drafts — with their own persisted `description` — before any Jira ticket exists, while everything downstream (Process Flow generation, comments, exports) keeps working off the stable internal `key`.

## Related
- [[Vinnstack Interrogation Room]]
