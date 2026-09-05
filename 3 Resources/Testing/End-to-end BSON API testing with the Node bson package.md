---
title: "End-to-end BSON API testing with the Node bson package"
created: 2026-08-25
type: howto
status: seedling
source: "session 2026-08-25"
tags: [bson, testing, e2e, node, api]
---

# End-to-end BSON API testing with the Node bson package

To black-box test a BSON JAX-RS API from Node: send Content-Type/Accept: application/bson, serialize the request object with the `bson` npm package (serialize(obj) -> Buffer) as the fetch body, and deserialize(Buffer.from(await res.arrayBuffer())) the response.

Assert type fidelity by round-tripping a Date and an ObjectId: after deserialize they must come back as a JS Date and an ObjectId, proving the server preserved native BSON types instead of stringifying them like the JSON path does.

Match the server wire shapes: lists are wrapped { items: [...] } (BSON has no top-level array), and scalars like count come back as { count: n }. One lifecycle script (create -> read -> query -> aggregate -> update -> delete -> verify-empty) on a throwaway collection covers every endpoint and cleans up. Node 18+ has global fetch; bson is the only dependency.

## Related

- [[BSON has no top-level array so a document list must be wrapped in a document]]
- [[Use a BSON endpoint instead of JSON to store MongoDB dates as native Date]]
