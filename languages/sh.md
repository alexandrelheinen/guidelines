# Shell

## Strict mode

Every non-trivial script starts with:

```bash
#!/usr/bin/env bash
set -euo pipefail
```

`-e` stops on the first failing command, `-u` fails on an unset variable
reference, `-o pipefail` makes a pipeline fail if any stage fails rather
than only the last one. Skipping this is how a script silently continues
past a failure and corrupts later steps.

## Fail fast, labeled errors

Print a clear step label before each phase, and a distinct failure message
that names what broke, since this matters for both CI logs and local
debugging:

```bash
step() { echo "==> $1"; }
fail() { echo "ERROR: $1" >&2; exit 1; }

step "Running tests"
pytest || fail "tests failed"
```

## One script, same in CI and local

The validation entry point (`./scripts/validate.sh` or similar) must be a
single script that runs identically whether invoked by a developer locally
or by CI: never a set of steps that are "the same in spirit" but
implemented twice. See
[workflow/integration.md](../workflow/integration.md#one-script-same-in-ci-and-local).

Where a project's validation needs differ by the size of a change (a
docs-only change does not need a full hardware smoke test), express that as
one script that inspects `git diff` and runs the right subset: not as
several scripts a human has to remember to pick between.

## Never echo secrets

No script should print a token, password, or credential to stdout/stderr,
even for debugging: that output ends up in CI logs, which are often less
access-controlled than the secret itself.

## Naming

Scripts use `snake_case.sh` or `kebab-case.sh`. Pick one per project and use
it consistently; do not mix within the same `scripts/` directory.
