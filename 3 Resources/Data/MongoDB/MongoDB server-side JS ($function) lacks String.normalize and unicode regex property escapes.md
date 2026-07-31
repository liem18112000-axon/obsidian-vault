---
title: "MongoDB server-side JS ($function) lacks String.normalize and unicode regex property escapes"
created: 2026-07-16
type: lesson
status: seedling
source: "session 2026-07-16"
tags: [mongodb, server-side-js, $function, mozjs, gotcha, luz-docs, ngram]
---

# MongoDB server-side JS ($function) lacks String.normalize and unicode regex property escapes

MongoDB aggregation `$function` (and `$accumulator`) run in the mongod's embedded MozJS engine, which is a RESTRICTED / older JS environment — not full modern Node/browser JS. Verified on dev luz-mongodb02 (MongoDB, x509-auth, `lang:"js"`):

- `Math.imul` → works.
- `String.prototype.normalize` (e.g. NFD Unicode normalization) → **NOT a function** ("normalize is not a function"). Absent.
- Regex Unicode property escapes with the `/u` flag, e.g. `/\p{M}+/gu` → **rejected** ("raw brace is not allowed in regular expression with unicode flag"). Not supported.
- The legacy in-pod `mongo` shell's JS engine is even older and rejects `\p{M}` at PARSE time, so you cannot even define such a function in the shell to test — pass the body as a STRING to `$function` and test via the server aggregation path instead.

Implication: you cannot faithfully port Java text normalization (`Normalizer.normalize(NFD)` + strip `\p{M}`) into a `$function`. Diacritic folding must be done another way (a hardcoded fold table, or done client-side in Java). This is why the luz-docs ngram trigram backfill could NOT become a one-shot server-side `$function updateMany` the way the parallelize `_shard` backfill did — shard assignment is a trivial `$rand` expression; trigram computation needs real string ops the engine lacks.

Test method (no secret scanning needed): exec into the mongod pod and authenticate with the pod's OWN x509 member cert already present at `/tmp/tls.pem` — `mongo --tls --tlsCertificateKeyFile /tmp/tls.pem --tlsCAFile /etc/mongodb-ssl/ca.crt --authenticationMechanism MONGODB-X509 --authenticationDatabase '$external'` — then run `db.aggregate([{$documents:[{}]},{$set:{t:{$function:{body:"...",args:[],lang:"js"}}}}])`. Set MSYS_NO_PATHCONV=1 on Git-Bash/Windows so kubectl-exec paths are not mangled.

## Related
[[luz-docs 800k eArchive performance test]]

## Related

- [[luz-docs 800k eArchive performance test]]
