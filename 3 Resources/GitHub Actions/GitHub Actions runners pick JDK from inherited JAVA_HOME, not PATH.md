---
title: "GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml work 2026-06-06"
tags: [github-actions, java, gradle, ci, gotcha]
---

# GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH

On GitHub Actions Ubuntu runners, the Java toolchain (Gradle, Maven, raw `java`) resolves the JDK from the inherited **`JAVA_HOME`** environment variable, not from whichever `java` is first on `PATH`. The runner ships several preinstalled JDKs and sets `JAVA_HOME` to a recent one.

So `apt`-installing a specific JDK (e.g. Amazon Corretto 11 via an `install-java.sh` script) is **not enough** to force that version — builds keep using the runner default because `JAVA_HOME` still points at it.

**Fix:** after installing, pin the version by appending to the workflow env files:

```bash
JAVA11_HOME="$(ls -d /usr/lib/jvm/java-11-amazon-corretto* 2>/dev/null | head -n1)"
[ -z "$JAVA11_HOME" ] && JAVA11_HOME="$(dirname "$(dirname "$(readlink -f "$(command -v javac)")")")"
echo "JAVA_HOME=$JAVA11_HOME" >> "$GITHUB_ENV"
echo "$JAVA11_HOME/bin"       >> "$GITHUB_PATH"
```

The `ls` glob is the deterministic primary; the `readlink -f $(command -v javac)` chain is the fallback. This matters whenever you reuse a VM-oriented install script in CI instead of the `actions/setup-java` action (which sets `JAVA_HOME` for you).

## Related

- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
