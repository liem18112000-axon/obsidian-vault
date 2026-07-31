---
title: "Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines"
created: 2026-06-10
type: gotcha
status: seedling
source: "luz-docs test debugging, 2026-06-10"
tags: [arquillian, wildfly, timeout, testing]
---

# Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines

Arquillian's managed WildFly container defaults to a 60-second startup timeout. On a loaded machine the JVM spawn + deployment can finish just past the limit, producing `TimeoutException: Managed server was not started within [60] s` even though the WildFly boot log looks completely healthy (HTTP listener up, datasource bound). Diagnose by reading the boot timestamps vs the test-class elapsed time.

Fix: `<property name="startupTimeoutInSeconds">${wildfly.startup.timeout:180}</property>` in arquillian.xml. Note the cascade: the first IT class that times out poisons Arquillian init, so every later IT class fails in ~0.001s with "Arquillian initialization has already been attempted" — only the FIRST failure is the real one.

Related: [[WSL relay processes squat common dev ports on Windows]]

## Related

- [[WSL relay processes squat common dev ports on Windows]]
