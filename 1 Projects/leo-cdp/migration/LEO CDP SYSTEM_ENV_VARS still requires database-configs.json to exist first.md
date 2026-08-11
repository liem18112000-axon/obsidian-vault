---
ai_hash: 8998da718bf0debe
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities:
- LEO CDP
- SYSTEM_ENV_VARS
- database-configs.json
- ArangoDB
- DatabaseConfigs.loadFromFile()
- ARANGODB_* env vars
- IllegalArgumentin('File is not found')
- runtimeEnvironment
- PRO
- leocdp-metadata.properties
- configs/PRO-database-configs.json
- integration tests
- live DB
- ARANGODB_HOST
- ARANGODB_PORT
- ARANGODB_USERNAME
- ARANGODB_PASSWORD
- ARANGODB_DATABASE
- configs/database-configs.json = {"configs":{}}
- gitignored
- Design smell
- Wall of NoClassDefFoundError on first test run = static-init IO, split unit from
  integration
source: LEO CDP local integration-test wiring, 2026-06-08
status: seedling
tags:
- leo-cdp
- arangodb
- config
- integration-tests
- gotcha
title: LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first
type: gotcha
---

# LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first

LEO CDP gotcha: even with mainDatabaseConfig=SYSTEM_ENV_VARS (which builds the ArangoDB connection from ARANGODB_* env vars), DatabaseConfigs.loadFromFile() still reads configs/database-configs.json FIRST and throws IllegalArgumentin('File is not found') if absent - the SYSTEM_ENV_VARS env-var branch sits AFTER the file read, so it is unreachable when the file is missing. Also runtimeEnvironment=PRO in leocdp-metadata.properties rewrites the path to configs/PRO-database-configs.json. To run integration tests against a live DB with env-var connection: (1) set runtimeEnvironment= empty, (2) drop a minimal configs/database-configs.json = {"configs":{}} (content irrelevant - the env branch overrides), (3) export ARANGODB_HOST/PORT/USERNAME/PASSWORD/DATABASE. Both files are gitignored. Design smell: a config 'use env vars' mode that still hard-requires the file it is meant to replace.

## Related

- [[3 Resources/Languages/Java/Wall of NoClassDefFoundError on first test run = static-init IO, split unit from integration]]

%% ai-graph-start %%

**Related notes:**
- [[Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[Wall of NoClassDefFoundError on first test run = static-init IO, split unit from integration]]
- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]
- [[Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)]]

**Relations:**
- LEO CDP — *uses* — SYSTEM_ENV_VARS
- SYSTEM_ENV_VARS — *requires* — database-configs.json
- SYSTEM_ENV_VARS — *builds connection from* — ARANGODB_* env vars
- SYSTEM_ENV_VARS — *configures* — ArangoDB
- DatabaseConfigs.loadFromFile() — *reads* — database-configs.json
- DatabaseConfigs.loadFromFile() — *throws* — IllegalArgumentin('File is not found')
- runtimeEnvironment=PRO — *in* — leocdp-metadata.properties
- runtimeEnvironment=PRO — *rewrites path to* — configs/PRO-database-configs.json
- integration tests — *run against* — live DB
- To run integration tests — *set* — runtimeEnvironment= empty
- To run integration tests — *drop* — configs/database-configs.json = {"configs":{}}
- To run integration tests — *export* — ARANGODB_HOST
- To run integration tests — *export* — ARANGODB_PORT
- To run integration tests — *export* — ARANGODB_USERNAME
- To run integration tests — *export* — ARANGODB_PASSWORD
- To run integration tests — *export* — ARANGODB_DATABASE
- ARANGODB_HOST — *is part of* — ARANGODB_* env vars
- ARANGODB_PORT — *is part of* — ARANGODB_* env vars
- ARANGODB_USERNAME — *is part of* — ARANGODB_* env vars
- ARANGODB_PASSWORD — *is part of* — ARANGODB_* env vars
- ARANGODB_DATABASE — *is part of* — ARANGODB_* env vars
- database-configs.json — *is* — gitignored
- configs/PRO-database-configs.json — *is* — gitignored
- LEO CDP — *has* — Design smell
- Design smell — *is* — config 'use env vars' mode that still hard-requires the file it is meant to replace
- LEO CDP — *related to* — Wall of NoClassDefFoundError on first test run = static-init IO, split unit from integration

%% ai-graph-end %%