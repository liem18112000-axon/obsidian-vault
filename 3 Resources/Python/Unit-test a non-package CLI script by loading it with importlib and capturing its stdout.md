---
title: "Unit-test a non-package CLI script by loading it with importlib and capturing its stdout"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04 fb-info-project test_license_admin_unit.py"
tags: [python, pytest, testing, argparse]
---

# Unit-test a non-package CLI script by loading it with importlib and capturing its stdout

To unit-test a standalone CLI script that lives outside any package (e.g. tools/foo.py with no __init__.py), load it by path with importlib instead of `import`:

```python
import importlib.util
spec = importlib.util.spec_from_file_location("foo", ROOT / "tools" / "foo.py")
foo = importlib.util.module_from_spec(spec); spec.loader.exec_module(foo)
```

Then call its internal functions directly, passing a hand-built `argparse.Namespace(...)` in place of parsed CLI args, and capture what it prints with `contextlib.redirect_stdout(io.StringIO())` (or pytest's `capsys`). This tests the real code path without spawning a subprocess and without turning the script into a package.

Applied in fb-info-project to test tools/license_admin.py: build a Namespace with key/user/tier, call `admin.sign(args)`, grab the last stdout line as the minted token, and feed it to the client verifier - an end-to-end operator->client check.

Related: [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]
