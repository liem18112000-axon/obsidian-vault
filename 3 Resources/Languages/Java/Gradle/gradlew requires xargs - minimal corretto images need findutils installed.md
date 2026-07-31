---
title: "gradlew requires xargs - minimal corretto images need findutils installed"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP migration R6/G2 local, 2026-06-07"
tags: [gradle, docker, corretto, gotcha, al2023]
---

# gradlew requires xargs - minimal corretto images need findutils installed

The `gradlew` launcher script (Gradle 7+) ends by exec-ing java through `xargs` and aborts with **'xargs is not available'** if missing. Minimal JDK base images often lack it - amazoncorretto:25 (AL2023) does not ship findutils, while older corretto:11 Dockerfiles that ran `yum install unzip tar gzip` worked by side effect of a fuller toolset. Fix: `RUN yum install -y findutils` in the build stage before invoking ./gradlew. Symptom signature: build fails in <1s at the gradlew RUN step with only 'xargs is not available' as output.

## Related

- [[gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly]]
