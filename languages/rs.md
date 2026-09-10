# Rust

Rust is the house language for hot paths, real-time pipelines, and any
component where a memory or arithmetic fault is not an acceptable failure
mode. This file sets the toolchain, the formatter, the lint tiers, and the
rules that a reviewer can point at.

Two external documents anchor the rules below. The
[Safety-Critical Rust Coding Guidelines](https://coding-guidelines.arewesafetycriticalyet.org/)
supply the safety rules and their rationale; the
[Linux kernel Rust coding guidelines](https://docs.kernel.org/rust/coding-guidelines.html)
supply the documentation and lint-management conventions. The consortium
document is an active draft: most of its chapters are still index pages,
so treat it as a source of individual rules worth adopting rather than a
standard this family claims conformance to. Where a rule below mirrors one
of theirs, the mapping is named so the reasoning stays traceable.

## Toolchain

- **Edition 2024**, resolver 3. New crates start there; an existing 2021
  crate migrates with `cargo fix --edition` the next time it is touched.
- Pin the toolchain in `rust-toolchain.toml` so a local build and CI agree
  on the compiler, and declare the same floor as `rust-version` in
  `Cargo.toml`.

```toml
# rust-toolchain.toml
[toolchain]
channel = "1.98"
components = ["rustfmt", "clippy", "llvm-tools-preview"]
```

- Commit `Cargo.lock`, for libraries as well as binaries. The usual
  argument for omitting it in a library assumes a published crate whose
  downstream users resolve their own versions; these projects are private
  and reproducibility matters more than that convention.
- One workspace per repository, crates under `crates/<name>/`, shared
  metadata and lints in `[workspace.package]` and `[workspace.lints]`.

## Formatting

`rustfmt` with a small `rustfmt.toml` at the workspace root:

```toml
edition = "2024"
max_width = 100
newline_style = "Unix"
```

`max_width = 100` is rustfmt's own default and it stays, even though the
C++ house style sits at 80 columns and Python at 88. Rust wraps generic
bounds, iterator chains, and `where` clauses badly under a narrower limit,
and every library a reader will cross-reference is formatted at 100.
Ecosystem consistency wins here; the cross-language column number is not
worth the wrapping.

Nothing else belongs in that file. The options worth wanting, mainly
`group_imports` and `imports_granularity`, remain nightly-only as of
September 2026, and a stable toolchain warns and ignores them. Import
grouping is therefore a convention the author applies and the reviewer
checks: `std` first, then external crates, then `crate`/`super`/`self`,
with a blank line between groups. Stable rustfmt sorts within each group
and leaves the blank lines alone, so the layout survives a reformat.

```rust
use std::collections::HashMap;
use std::sync::Arc;

use serde::Deserialize;
use tokio::sync::mpsc;

use crate::config::Config;
use crate::error::PipelineError;
```

A project may add a non-blocking `cargo +nightly fmt --check` job with
`unstable_features = true` if it wants that grouping enforced, but the
blocking gate stays on stable.

## Lints

Lint configuration lives in the `[workspace.lints]` table of the root
`Cargo.toml`, not in crate-root attributes and not in CI flags, so that
one file answers what the rules are. Every crate opts in with:

```toml
[lints]
workspace = true
```

Group entries need `priority = -1`, otherwise the group overrides the
individual lints listed after it.

### Baseline, every crate

```toml
[workspace.lints.rust]
unsafe_op_in_unsafe_fn = "deny"
missing_docs = "warn"
missing_debug_implementations = "warn"
unreachable_pub = "warn"
unused_qualifications = "warn"
unused_lifetimes = "warn"
trivial_casts = "warn"
trivial_numeric_casts = "warn"
let_underscore_drop = "warn"

[workspace.lints.clippy]
all = { level = "deny", priority = -1 }
pedantic = { level = "warn", priority = -1 }
undocumented_unsafe_blocks = "deny"
multiple_unsafe_ops_per_block = "deny"
unwrap_used = "deny"
expect_used = "warn"
panic = "warn"
todo = "warn"
unimplemented = "warn"
dbg_macro = "deny"
float_cmp = "deny"
missing_errors_doc = "warn"
missing_panics_doc = "warn"
allow_attributes = "warn"
allow_attributes_without_reason = "warn"
```

### Hardened, for real-time, unsafe, and FFI crates

Add these in the crate that needs them, and say in its crate-level docs
why it is hardened. They are restriction lints, loud by design, and
turning them on across an ordinary application crate produces noise rather
than safety.

```toml
[lints.clippy]
arithmetic_side_effects = "deny"
as_conversions = "deny"
indexing_slicing = "deny"
integer_division = "deny"
modulo_arithmetic = "deny"
cast_possible_truncation = "deny"
cast_sign_loss = "deny"
cast_precision_loss = "deny"
large_stack_arrays = "deny"
string_slice = "deny"
exit = "deny"
```

Each one has a counterpart in the consortium guidelines:
`as_conversions` for "The 'as' operator should not be used with numeric
operands", `arithmetic_side_effects` for "Ensure that integer operations
do not result in arithmetic overflow", `integer_division` and
`modulo_arithmetic` for "Do not divide by 0", `indexing_slicing` for
panic freedom on caller-supplied indices.

### Suppressing a lint

Prefer `#[expect(...)]` over `#[allow(...)]`, as the kernel guidelines
require: the compiler reports an `expect` that no longer fires, so a
suppression cannot outlive the problem it covered. Reach for `allow` only
where the warning appears under some configurations and not others, which
is mostly conditional compilation and macro expansion. Either way, a
suppression carries a `reason`, and the reason names the constraint rather
than restating the lint:

```rust
#[expect(clippy::cast_possible_truncation, reason = "frame width is bounded to u16 by the sensor")]
let width = frame.width as u16;
```

That requirement is the Rust form of the escape-hatch rule in
[style/comments.md](../style/comments.md#anyescape-hatch-justification).

## Unsafe code

- A crate that does not need `unsafe` says so at its root:
  `#![forbid(unsafe_code)]`. This is the default state, and most crates in
  a project should hold it.
- A crate that does need `unsafe` confines it to one named module (`ffi`,
  `raw`, `sys`), keeps that module small enough to audit in one sitting,
  and exposes a safe API above it. The rest of the crate stays under
  `#![deny(unsafe_code)]` with the module carrying an `expect`.
- Every `unsafe` block carries a `// SAFETY:` comment immediately above it
  stating the invariant that makes the operation sound, not what the code
  does. `clippy::undocumented_unsafe_blocks` enforces the presence of the
  comment; a reviewer enforces that it says something.
- One unsafe operation per block, via
  `clippy::multiple_unsafe_ops_per_block`, so that each SAFETY comment
  maps to exactly one obligation instead of a list a reader has to split
  apart.
- Never expand an `unsafe` block out of a macro. A reader scanning for the
  `unsafe` keyword has to be able to find every one of them, which is the
  consortium's "Assure visibility of `unsafe` keyword in unsafe code" and
  "Do not hide unsafe blocks within macro expansions".
- Every `unsafe fn` and unsafe trait documents its caller obligations in a
  `# Safety` section. The section states preconditions the caller must
  guarantee; the SAFETY comment inside a block states why those
  preconditions hold at that call site. They are different claims and the
  distinction matters.
- Any crate containing `unsafe` runs its test suite under Miri:
  `cargo +nightly miri test`. A test that Miri cannot execute (FFI into a
  real library, for instance) is gated with
  `#[cfg_attr(miri, ignore)]` and a reason.

```rust
/// Returns the sample at `index` without a bounds check.
///
/// # Safety
///
/// `index` must be less than `self.len()`.
pub unsafe fn sample_unchecked(&self, index: usize) -> f32 {
    // SAFETY: the caller guarantees index < self.len(), and self.data holds
    // exactly self.len() initialized samples.
    unsafe { *self.data.get_unchecked(index) }
}
```

## Panics, arithmetic, and conversions

Library code does not panic on anything a caller could have caused. That
rules out `unwrap`, bare indexing on caller-supplied indices, and slicing
by a range that came in from outside. `expect` is acceptable where a panic
is genuinely the correct response to a broken internal invariant, and its
message states the invariant rather than the symptom:
`.expect("channel capacity is non-zero, checked in Config::validate")`.
Test code is exempt: a test module carries
`#![expect(clippy::unwrap_used, reason = "test assertions")]`.

Arithmetic on values that came from outside the crate names its overflow
behavior at the point of use, with `checked_*`, `saturating_*`, or
`wrapping_*`. Choosing one is a design decision and the choice should be
visible in the code, which is what `clippy::arithmetic_side_effects`
forces in a hardened crate.

Release builds keep overflow checks on:

```toml
[profile.release]
overflow-checks = true
debug = "line-tables-only"
```

Silent wrapping in a control loop or a buffer index is a real fault, and a
panic at least surfaces it. A project with a hard real-time budget that
cannot absorb the check turns it off deliberately, records the decision in
`docs/decisions.md`, and adopts the hardened lint tier in exchange.

Numeric conversions go through `From` and `TryFrom`, never `as`. `as`
truncates, rounds, and changes sign silently, and it communicates nothing
about whether the author expected the conversion to be lossless.
`TryFrom` in the fallible direction turns that expectation into a
`Result` the caller has to handle.

## Error handling

See [style/errors.md](../style/errors.md) for the cross-language strategy.
Rust specifics:

- A library defines its own error enum with `thiserror`, marks it
  `#[non_exhaustive]`, and returns it from every fallible public function.
  `Box<dyn Error>` in a public library signature is not acceptable: it
  erases exactly the information a caller needs to branch on.
- A binary may use `anyhow` or `eyre`, and only at the top layer where the
  error is about to be reported to a human. It does not leak into the
  library crates below it.
- Error variants describe the failure, not the function that produced it:
  `FrameTooLarge { bytes: usize }`, not `PipelineRunError`.
- `Drop` implementations do not panic, matching the rule that destructors
  never throw.
- Every fallible public function documents its failure modes in an
  `# Errors` section, enforced by `clippy::missing_errors_doc`.

```rust
#[derive(Debug, thiserror::Error)]
#[non_exhaustive]
pub enum PipelineError {
    #[error("frame of {bytes} bytes exceeds the {limit} byte buffer")]
    FrameTooLarge { bytes: usize, limit: usize },

    #[error("inference backend rejected the input tensor")]
    Backend(#[from] BackendError),
}
```

## Documentation

Documentation is `rustdoc` in Markdown, `///` on items and `//!` on
modules and crates. Following the kernel guidelines, document private
items too and not only the public surface: `missing_docs` only reaches the
public API, so the rest is a review expectation.

- The first paragraph is one sentence describing the item, ending with a
  period. Detail goes in the paragraphs after it.
- Sections, in this order when they apply: `# Safety`, `# Errors`,
  `# Panics`, `# Examples`.
- Examples are doc tests and they run in CI, so they stay compiling.
- Link items with brackets (`[Config::validate]`) rather than naming them
  in plain text, so the reference survives a rename check.
- Ordinary `//` comments are Markdown too, capitalized and ending with a
  period, which keeps a comment cheap to promote into documentation later.
- `cargo doc` runs with `RUSTDOCFLAGS="-D warnings"`: a broken intra-doc
  link fails the build.

See [style/comments.md](../style/comments.md#public-api-documentation-is-mandatory-at-librarymodule-boundaries)
for what the family expects of a public API in any language.

## Naming

[style/naming.md](../style/naming.md) holds the cross-language rules.
Rust specifics:

| Construct | Convention |
|---|---|
| Types, traits, enum variants | `PascalCase` |
| Functions, methods, variables, modules | `snake_case` |
| Constants and statics | `SCREAMING_SNAKE_CASE` |
| Lifetimes | short lowercase: `'a`, `'src` |
| Generic type parameters | single capital or short `PascalCase`: `T`, `Frame` |
| Crates | `kebab-case` on disk, `snake_case` when imported |
| Files | `snake_case`, matching the module they define |

Do not repeat the module name inside an item name. The path already
carries it, so `gpio::LineDirection::In` reads better than
`gpio::GpioLineDirection::GPIO_LINE_DIRECTION_IN`. When wrapping a C API,
mirror the C names with the casing adjusted and the namespace prefix
dropped, which is the kernel rule and keeps the correspondence obvious to
someone reading both sides.

Use a newtype rather than a bare primitive for a value with a unit or a
domain meaning, which is the consortium's "Use strong types to
differentiate between logically distinct values" and the Rust form of the
`who_what` rule in
[style/naming.md](../style/naming.md#the-who_what-rule-for-physical-and-measured-variables).
A `struct FrameIndex(u32)` cannot be passed where a `struct SampleCount(u32)`
belongs, and the compiler catches at build time what a naming convention
only catches at review.

## Testing

Rust uses co-located unit tests plus integration tests against the public
API, which is the per-project layout choice
[workflow/tdd.md](../workflow/tdd.md#test-layout) asks each project to
make once:

- Unit tests in `#[cfg(test)] mod tests` at the bottom of the file they
  cover, with access to private items.
- Integration tests in `tests/`, exercising only the public API, one file
  per feature area.
- Benchmarks in `benches/` with Criterion, for anything with a latency
  budget.

`cargo nextest run` is the runner: it isolates each test in its own
process, which matters as soon as a test touches a global or an FFI
library. It does not run doc tests, so `cargo test --doc` runs alongside
it rather than instead of it.

Coverage comes from `cargo llvm-cov`, gated at the 80-90% range described
in [workflow/tdd.md](../workflow/tdd.md#coverage).

Property tests with `proptest` earn their place on parsers, arithmetic on
external input, and anything with a round-trip invariant. Reach for one
where the input space is larger than the examples you would think to
write.

An unimplemented stub is `todo!()`, with its test marked
`#[should_panic(expected = "not yet implemented")]` so the suite fails
loudly until the real code lands, and the marker has to be removed in the
same commit.

## Dependencies

- No wildcard versions. A dependency is added with a caret requirement and
  a reason a reviewer can evaluate.
- `default-features = false` wherever the default set pulls in more than
  the crate uses.
- `cargo deny check` gates advisories, licenses, duplicate versions, and
  sources, with `deny.toml` committed at the workspace root.
- `cargo machete` catches dependencies that stopped being used.
- `cargo semver-checks` runs on any crate another repository consumes, so
  a breaking change is caught before it is tagged rather than after.

## FFI

- `#[repr(C)]` on every type crossing the boundary, and `core::ffi` types
  (`c_int`, `c_char`) rather than assuming a width.
- Edition 2024 requires `unsafe extern "C"` blocks, which is the right
  default: declaring a foreign signature is itself a safety claim.
- A panic must not unwind across the boundary. Wrap the Rust side of every
  exported function in `std::panic::catch_unwind` and convert to an error
  code.
- Every exported function documents ownership: who allocates, who frees,
  how long a returned pointer stays valid, and whether null is accepted.
- Check every incoming pointer for null before dereferencing it, and say
  in the `# Safety` section what else the caller has to guarantee.
- Generate the C header with `cbindgen` in `build.rs` rather than
  maintaining it by hand and letting it drift.

## Required tooling

| Tool | Purpose | Gate |
|---|---|---|
| `rustfmt` | Formatting | Blocking |
| `clippy` | Lints, at the tiers above | Blocking |
| `cargo-nextest` | Test runner | Blocking |
| `cargo-llvm-cov` | Coverage | Blocking, 80-90% |
| `cargo-deny` | Advisories, licenses, bans, sources | Blocking |
| `rustdoc` | Docs and doc tests, warnings denied | Blocking |
| `miri` | Undefined behavior, crates with `unsafe` | Blocking for those crates |
| `cargo-machete` | Unused dependencies | Advisory |
| `cargo-semver-checks` | API breakage, consumed crates | Blocking for those crates |
| `typos` | Spelling in code and docs | Advisory |

Every project wires these into one script (`scripts/validate.sh` or
equivalent) that a contributor runs before pushing and CI runs as its
only Rust step, so the two cannot disagree:

```bash
cargo fmt --all --check
cargo clippy --all-targets --all-features -- -D warnings
cargo nextest run --all-features
cargo test --doc
RUSTDOCFLAGS="-D warnings" cargo doc --no-deps --all-features
cargo deny check
cargo llvm-cov --all-features --fail-under-lines 80
```

## Reference bibliography

- [Safety-Critical Rust Coding Guidelines](https://coding-guidelines.arewesafetycriticalyet.org/),
  Safety-Critical Rust Consortium: rule categories, rationale, and the
  compliant and non-compliant example format this file borrows its safety
  rules from.
- [Linux kernel Rust coding guidelines](https://docs.kernel.org/rust/coding-guidelines.html):
  documentation conventions, the `expect` over `allow` rule, and the
  C-name mirroring rule.
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/):
  naming, interoperability, and the checklist for a public API.
- [The Rustonomicon](https://doc.rust-lang.org/nomicon/): the reference
  for what an `unsafe` block actually promises.
- [Rust Style Guide](https://doc.rust-lang.org/style-guide/): what
  `rustfmt` implements, for the cases it cannot format automatically.
