# Test-Driven Development (TDD)

Test-driven development is the default, not an optional practice reserved
for "important" code. Do not land behavior without a test that proves it.

## The cycle

1. **Red**: write a failing test that expresses the behavior you are about
   to add. It must fail for the right reason (confirm this before moving
   on: a test that passes immediately, or fails on an unrelated error,
   proves nothing).
2. **Green**: write the minimal code that makes the test pass. Resist
   adding anything the test does not require yet.
3. **Refactor**: clean up duplication or awkward structure now that the
   behavior is locked in by a passing test, without changing behavior.

For regressions specifically: write the failing test that reproduces the
bug first, then fix it. This is non-negotiable: a bug fix without a
regression test can silently come back.

## Coverage

Projects in this family target somewhere in the **80-90% line/branch
coverage** range on core library code, enforced in CI (`--cov-fail-under`
or equivalent). Treat the exact number as a per-project choice, not a
universal constant, but do not merge a project with no coverage gate at
all. Test directories and throwaway scripts are typically excluded from
the gate.

Do not lower a coverage gate to make CI pass. If a gate is genuinely wrong
for a specific case, that is a decision worth a line in `docs/decisions.md`,
not a silent threshold edit.

## Marking intentionally unimplemented work

Never leave a stub that silently returns success. Mark it so the test
suite fails loudly until the real implementation lands:

- Python: raise `NotImplementedError` in the stub, and mark its test
  `@pytest.mark.xfail(strict=True, raises=NotImplementedError)`: `strict`
  means the marker itself fails once the stub starts passing, forcing you
  to remove the marker rather than forget it.
- C++: `GTEST_SKIP()` with a descriptive reason string.

Remove the marker in the same commit that lands the real implementation.
Do not let a codebase accumulate permanently skipped tests.

## No silent skips

A skipped or expected-to-fail test must always carry a reason that names
what is blocking it (a requirement id, an issue link, or a one-line
explanation): never a bare `@pytest.mark.skip` with no argument. If your
test framework supports strict-marker enforcement (pytest's
`--strict-markers`), turn it on.

## Test layout

Tests mirror the source tree: `tests/foo/bar_test.py` for
`src/pkg/foo/bar.py`, or co-located `foo.test.ts` next to `foo.ts` for
JS/TS. Pick one convention per project and apply it uniformly: do not mix
mirrored and co-located layouts in the same codebase.

## Test naming

See [naming.md](../style/naming.md#test-naming): a test's name should read
as a spec sentence, not describe its inputs.
