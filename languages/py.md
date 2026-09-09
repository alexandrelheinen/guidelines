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

## Reference bibliography

PEP 8 (style), PEP 257 (docstrings), PEP 484 (type hints), PEP 561
(distributing type information), PEP 621 (project metadata in
`pyproject.toml`), PEP 517 (build backend interface).
