---
title: "Reused Mongo port-forward can hit a secondary - writes need hello primary lookup"
created: 2026-07-22
type: lesson
status: seedling
source: "canary tenant unset 2026-07-22"
tags: [mongodb, kubectl, gotcha, luz]
---

# Reused Mongo port-forward can hit a secondary - writes need hello primary lookup

A reused "localhost:27017 already reachable" kubectl port-forward to a Mongo replica set may point at a SECONDARY — reads work (stats scripts pass) but writes fail with "not primary" even with readPreference=primary in the URI, because directConnection=true pins the driver to that one member. Find the primary from any member with `db.command({hello:1})` → `.primary` (pod DNS name), port-forward that pod on a fresh port, and run the write there. Tenant-db auth convention on dev Luz mongo: user=pass=authSource=db=tenantId.
