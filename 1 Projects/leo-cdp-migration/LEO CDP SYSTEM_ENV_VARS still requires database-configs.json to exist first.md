---
title: "LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first"
created: 2026-06-08
type: gotcha
status: seedling
source: "LEO CDP local integration-test wiring, 2026-06-08"
tags: [leo-cdp, arangodb, config, integration-tests, gotcha]
---

# LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first

LEO CDP gotcha: even with mainDatabaseConfig=SYSTEM_ENV_VARS (which builds the ArangoDB connection from ARANGODB_* env vars), DatabaseConfigs.loadFromFile() still reads configs/database-configs.json FIRST and throws IllegalArgumentin('File is not found') if absent - the SYSTEM_ENV_VARS env-var branch sits AFTER the file read, so it is unreachable when the file is missing. Also runtimeEnvironment=PRO in leocdp-metadata.properties rewrites the path to configs/PRO-database-configs.json. To run integration tests against a live DB with env-var connection: (1) set runtimeEnvironment= empty, (2) drop a minimal configs/database-configs.json = {"configs":{}} (content irrelevant - the env branch overrides), (3) export ARANGODB_HOST/PORT/USERNAME/PASSWORD/DATABASE. Both files are gitignored. Design smell: a config 'use env vars' mode that still hard-requires the file it is meant to replace.

## Related

- [[Wall of NoClassDefFoundError on first test run = static-init IO]]
- [[split unit from integration]]
