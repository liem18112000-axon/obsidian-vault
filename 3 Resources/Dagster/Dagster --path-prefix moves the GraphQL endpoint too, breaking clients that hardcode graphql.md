---
title: "Dagster --path-prefix moves the GraphQL endpoint too, breaking clients that hardcode /graphql"
created: 2026-08-20
type: gotcha
status: seedling
source: "leo-customer360 dagster expose decision, 2026-08"
tags: [dagster, graphql, reverse-proxy, path-prefix, gotcha]
---

# Dagster --path-prefix moves the GraphQL endpoint too, breaking clients that hardcode /graphql

To serve the Dagster webserver UI under a sub-path (e.g. behind a reverse proxy at /dagster), you must launch it with `--path-prefix /dagster`. But that flag relocates EVERYTHING the webserver serves, including the **GraphQL endpoint**: `/graphql` -> `/dagster/graphql`. 

Gotcha: the official `dagster_graphql.DagsterGraphQLClient(hostname, port_number=...)` builds its URL as `host:port/graphql` with NO path-prefix option, and backends usually hit Dagster **directly** on its port (not through the proxy). So adding `--path-prefix` for the UI silently breaks any programmatic GraphQL caller that assumed `/graphql`. To keep both, you must repoint the client (custom gql transport with url=`.../dagster/graphql`).

Design consequence: if a service already talks to Dagster GraphQL, prefer exposing the UI on a **subdomain** (Dagster stays at root, no --path-prefix, client untouched) rather than a path. Or just leave Dagster on its raw port (IP / SSH tunnel) and dont front it at all.

Related: [[HSTS on a parent domain makes all plain-HTTP and self-signed ports on a subdomain unreachable in-browser]], [[Stripping a path prefix at the proxy breaks framework auto-redirects; forward it un-stripped when the app has root_path]]
