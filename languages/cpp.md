# C++

## Standard and build

CMake 3.16+ floor (see [cmake.md](cmake.md)). C++17 or C++20 is a
per-project choice — pick one at project start and don't mix standards
across targets in the same repo.

## Formatting

`.clang-format`, LLVM-based:

```yaml
BasedOnStyle: LLVM
IndentWidth: 4
ColumnLimit: 80
PointerAlignment: Right
BreakBeforeBraces: Attach
SortIncludes: CaseSensitive
Standard: Latest
```

Run via a project script (`scripts/clang.sh` or equivalent) that applies
`clang-format -i` to first-party sources only (exclude `external/`,
vendored code). CI checks formatting with `git diff --exit-code` after
running the formatter — any diff fails the check.

This is the canonical house style. At least one existing repo uses a
different set of parameters (2-space indent, 100 columns, pointer-left,
unsorted includes) — treat that as a known deviation to reconcile, not a
second accepted style.

## Naming

See [style/naming.md](../style/naming.md). C++ specifics:

- Classes/types: `PascalCase`
- Functions, methods, variables: `snake_case`
- Private/protected members: `trailing_underscore_`
- Constants and macros: `UPPER_CASE` (project-specific macros get a project
  prefix, e.g. `BOSSA_MAX_RETRIES`)
- Namespaces mirror the directory structure: `include/pkg/io/` →
  `namespace pkg::io`
- File names: `snake_case`, matching the class they define
  (`gpio_controller.h` for `class GPIOController`)

## Documentation

Doxygen `/** */` blocks mandatory on public headers — see
[style/comments.md](../style/comments.md#public-api-documentation--required-for-librarymodule-boundaries)
for required tags. Private implementation functions may use brief inline
comments instead.

## Error handling

Layer-appropriate strategy — see
[style/errors.md](../style/errors.md#error-strategy-by-layer-cc-embedded-and-similarly-latency-sensitive-code).
No exceptions or heap allocation in driver hot paths; result types or
return codes instead. RAII for all resource cleanup; destructors never
throw.

## Testing

GTest, tests mirror the `include/`/`src/` structure, run via `ctest`.
Catch2 is acceptable where a project already uses it, but GTest is the
default for new projects. Header + test files for a module land in the
same commit (see [workflow/tdd.md](../workflow/tdd.md)).

## Defensive programming baseline

Validate every syscall return value, bound all inputs, fail fast with a
logged error rather than a silent default, and keep signal handlers to
`volatile sig_atomic_t` flag assignment only.
