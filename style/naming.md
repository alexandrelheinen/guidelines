# Naming

Cross-language naming rules — conventions that apply regardless of which
language a project uses. Language-specific casing and idioms live in
`languages/`; this file only covers what should be consistent everywhere.

## Physical / measured variables — the `who_what` rule

Applies to any variable that represents a physical quantity or measurement.

- Name the quantity, not the unit: `max_speed`, not `speed_ms`.
- Qualifiers (`max`, `min`, `current`, `target`) go first: `max_temperature`,
  not `temperature_max`.
- Integer counts get a `_count` suffix: `sensor_count`, not `num_sensors`.
- Collections are pluralized, not suffixed with `_list`: `ring_radii`, not
  `ring_radius_list`.
- Exception: config file keys (YAML/JSON) and inline comments may keep a
  unit suffix for human readability, since they're read standalone rather
  than inside an expression (`timeout_ms: 500  # milliseconds` is fine).

## Language

US English only, everywhere: identifiers, comments, docs, commit messages,
log strings, error messages. Common corrections:

| UK | US |
|---|---|
| colour | color |
| behaviour | behavior |
| optimise / initialise / analyse | optimize / initialize / analyze |
| cancelled | canceled |
| grey | gray |
| centre | center |
| catalogue | catalog |
| licence (noun) | license |

Exception: a parameter or field name that belongs to an external library or
API keeps that library's spelling (e.g. `tqdm(colour=...)`).

## Casing by construct

Defaults across the C-family, Python, Rust, Ruby, and shell; JS/TS and C#
follow their own ecosystem norms (`camelCase` methods/variables) — see
`languages/js.md`, `languages/ts.md`, `languages/cs.md`.

| Construct | Convention |
|---|---|
| Types / classes | `PascalCase` |
| Functions, methods, variables | `snake_case` |
| Constants, macros | `UPPER_CASE` |
| Private/protected members (C++) | trailing underscore: `count_` |
| Private members (Python) | leading underscore: `_count` |
| Namespaces / packages | `snake_case`, mirroring the directory they live in (`src/io/` → `namespace io`) |

## Intentionally unused parameters

Prefix with `_` (`_event`, `_unused`) rather than using the value and
discarding it. This is enforced today via ESLint's
`argsIgnorePattern: '^_'`; apply the same convention by hand in languages
without a linter that checks it.

## File naming

A file's name matches the primary type or function it defines, in that
language's casing convention — for C/C++, that means `snake_case` filenames
even for a `PascalCase` class (`gpio_controller.h` defines `GPIOController`).
This is a known open inconsistency: at least one existing C++ repo uses
`PascalCase` filenames instead. Treat `snake_case` as the house style going
forward and reconcile the outlier when it's next touched, rather than
mixing conventions in new code.

## Test naming

Prefer a test name that reads as a spec sentence. Two accepted forms,
depending on the framework's idiom:

- xUnit-style: `MethodUnderTest_Scenario_ExpectedBehavior`
  (`WriteThenRead_RoundTripsValue`).
- BDD-style: `test_<behavior>` (pytest) or `it("does X")` (JS test
  runners).

Don't name a test after its inputs (`test_case_1`) — name it after the
behavior it proves.

## User-facing text

No em dashes in product names, titles, or headings — use `|` for title
segments (`Freshy | Cooling Map`), a hyphen for compounds, or a comma. This
is a text-content rule, not a code-naming rule, but it governs the naming of
user-facing strings the same way the rules above govern identifiers.
