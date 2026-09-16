# Python

## Toolchain

- Target Python **3.12**, `requires-python = ">=3.12"` (or `>=3.12,<3.13`
  when pinning tightly).
- `src/` layout: package code under `src/<package>/`, tests under `tests/`.
- All tool configuration lives in `pyproject.toml`: do not add a parallel
  `.flake8`/`setup.cfg`/`tox.ini` unless there is a specific tool that cannot
  read `pyproject.toml` yet.

## Formatting and linting

```toml
[tool.black]
line-length = 88
target-version = ["py312"]

[tool.isort]
profile = "black"
line_length = 88
known_first_party = ["<package>"]

[tool.ruff]
target-version = "py312"
line-length = 88
select = ["E", "W", "F", "I", "UP", "B", "SIM"]

[tool.mypy]
strict = true
python_version = "3.12"
```

`line-length = 79` is an accepted per-project override for repos already
shipping at that width: do not reformat an existing 79-column project to
88 just for consistency's sake.

## Docstrings

Google-style, required on every public module, class, and function. See
[style/comments.md](../style/comments.md) for the exact section format
(`Args`/`Returns`/`Raises`/`Yields`) and when a one-liner is enough.

## Type hints

Full type hints on public functions and class attributes; `mypy --strict`
in CI. `# type: ignore` requires a one-line comment explaining why (see
[style/comments.md](../style/comments.md#anyescape-hatch-justification)).

## Testing

`pytest`, tests mirror the `src/` package structure. Coverage via
`pytest-cov`, gate at 80-90% depending on project (see
[workflow/tdd.md](../workflow/tdd.md#coverage)). Use `--strict-markers
--strict-config` so a misspelled or unregistered marker fails loudly instead of
being silently ignored.

Tests that require an optional heavy dependency (a display backend, a GPU
library) should skip gracefully rather than crash headless CI collection:

```python
pygame = pytest.importorskip("pygame")
```

## Naming

See [style/naming.md](../style/naming.md) for the cross-language rules.
Python-specific casing: modules and packages `snake_case`, classes
`PascalCase`, functions/methods/variables `snake_case`, constants
`UPPER_CASE`, private members `_leading_underscore`.

## Errors

Build a small domain-specific exception hierarchy rooted at one base
exception per package, rather than raising bare `Exception`/`ValueError`
everywhere. See [style/errors.md](../style/errors.md#exceptions-vs-result-types-application-code).

## Critical systems

Applies to a module at criticality C1 or C2 in
[workflow/criticality.md](../workflow/criticality.md). The cross-language
rules are in [style/defensive.md](../style/defensive.md).

**Decide whether Python is product code or tooling, and write the decision
down.** Under the tool classifications in IEC 61508-4 and ISO 26262-8,
Python used for test harnesses, offline analysis, and scripting sits in
the class that cannot introduce an error into the delivered artifact, and
the practices in this file are enough. Python inside a guidance or control
loop is delivered code, with no qualification path available to it. Either
position is defensible for a project nobody is certifying; only the
unexamined position is not. Record it in `docs/decisions.md`.

**`assert` is not a production check.** The language reference is explicit
that the code generator emits nothing for an `assert` statement under
`-O`. Use `assert` for internal invariants during development, and an
explicit `raise` for anything that must hold when the code ships.
Validation of arguments crossing a public API is always a `raise`.

**Pin every tolerance explicitly.** Four commonly used comparisons carry
four different defaults, so a test silently inherits whichever library it
happened to import:

| API | Formula | Defaults | Symmetric |
|---|---|---|---|
| `math.isclose` | `abs(a-b) <= max(rel_tol * max(abs(a), abs(b)), abs_tol)` | `rel_tol=1e-09`, `abs_tol=0.0` | Yes |
| `numpy.isclose` | `abs(a-b) <= atol + rtol * abs(b)` | `rtol=1e-05`, `atol=1e-08` | No |
| `numpy.testing.assert_allclose` | As above | `rtol=1e-07`, `atol=0` | No |
| `pytest.approx` | Relative to the expected value, or absolute | `rel=1e-6`, `abs=1e-12` | No |

Pass the tolerance at every call site, from a named domain constant in
physical units. Never rely on a default.

**Type hints are a boundary, not a guarantee.** `mypy --strict` catches
what it can at rest, and a value arriving from an untyped caller is still
unchecked at run time. Validate at the public boundary regardless of the
annotation.

**Bounded work.** Every search, sampling, or solving entry point takes an
explicit budget and returns a result that distinguishes convergence from
budget exhaustion, per
[style/defensive.md](../style/defensive.md#bounded-resources).

## Reference bibliography

PEP 8 (style), PEP 257 (docstrings), PEP 484 (type hints), PEP 561
(distributing type information), PEP 621 (project metadata in
`pyproject.toml`), PEP 517 (build backend interface).
