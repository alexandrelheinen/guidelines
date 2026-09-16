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

## Project layout

- A single-crate project keeps the standard `src/lib.rs` or `src/main.rs`
  layout, and grows into the workspace above once a second crate appears.
- Split crates along the dependency layering the project wants enforced.
  Cargo rejects a cycle between crates, so the split is what stops an
  accidental upward dependency from compiling, and that is a reason to
  split rather than a cost of splitting.
- A library crate stays usable without pulling a binary's dependencies:
  put binaries in `src/bin/` or in their own crate, never behind a default
  feature that drags a command-line parser into every consumer.
- Integration tests live in `tests/`, benchmarks in `benches/`, examples
  in `examples/`. Those are Cargo's own directories, and moving them buys
  nothing.

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
- In a workspace, each crate defines its own error enum and every one of
  them converts into a single root error the top-level crate exposes. A
  caller then matches on one type without the leaf crates depending on
  each other.
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
- Sections, in this order when they apply: `# Arguments`, `# Returns`,
  `# Safety`, `# Errors`, `# Panics`, `# Examples`.
- Examples are doc tests and they run in CI, so they stay compiling.
- Link items with brackets (`[Config::validate]`) rather than naming them
  in plain text, so the reference survives a rename check.
- Ordinary `//` comments are Markdown too, capitalized and ending with a
  period, which keeps a comment cheap to promote into documentation later.
- `cargo doc` runs with `RUSTDOCFLAGS="-D warnings"`: a broken intra-doc
  link fails the build.

Those headings are how the Google-style docstring sections that
[style/comments.md](../style/comments.md) requires elsewhere are spelled
in rustdoc:

| Google-style section | Rustdoc heading |
|---|---|
| summary line | first line of the `///` block |
| `Args` | `# Arguments` |
| `Returns` | `# Returns` |
| `Raises` | `# Errors`, for anything returned as `Err` |
| `Raises`, for a caller bug | `# Panics` |
| usage example | `# Examples`, written as a doctest |

A one-line comment is enough for an item whose signature already says
everything. Anything taking a physical quantity states its unit in the
comment, since the `who_what` rule in
[style/naming.md](../style/naming.md#the-who_what-rule-for-physical-and-measured-variables)
keeps the unit out of the identifier:

```rust
/// Advances the vehicle state by one control step.
///
/// # Arguments
///
/// * `state` - Current pose as `[x, y, heading]`, meters and radians.
/// * `max_speed` - Upper speed bound, meters per second.
/// * `dt` - Integration step, seconds.
///
/// # Returns
///
/// The propagated pose, in the same layout as `state`.
///
/// # Errors
///
/// Returns [`Error::InvalidDimension`] when `state` is not length 3.
pub fn step(state: &[f64], max_speed: f64, dt: f64) -> Result<[f64; 3], Error> {
```

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

`PascalCase` capitalizes only the leading letter of an acronym:
`HttpClient`, `KdTree`, `RrtPlanner`, `TlsConfig`. Full capitalization
reads as several separate words to `clippy`, which flags it under
`upper_case_acronyms`, and it leaves a name carrying two acronyms
unparseable. A crate presenting its types to another language keeps that
language's spelling at the boundary instead of renaming the Rust type,
which under PyO3 is `#[pyclass(name = "RRTPlanner")]` and leaves the
caller's import untouched.

Name a trait after the role it describes, in the vocabulary the project
already uses: `Planner`, `Sampler`, `CostTerm`. No `T` prefix, and no
`-able` suffix forced onto a name that does not want one. When a trait
replaces an interface declared in another language, whether a Python
`Protocol`, a TypeScript interface, or a C++ abstract base, keep that
interface's name: a one-to-one mapping between the two declarations is
worth more than a marginally more idiomatic name.

A crate being ported from another language may mirror the source module
names for the duration of the port, so the two trees can be read side by
side. Record that as a temporary deviation in the project's own guideline
file and revisit it once the old sources are gone.

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

A test name reads as a spec sentence and drops the `test_` prefix, since
`#[test]` already marks the function and repeating the marker in the name
says nothing: `fn plans_around_a_blocking_obstacle()` rather than
`fn test_plan()`. See
[style/naming.md](../style/naming.md#test-naming).

An unimplemented stub is `todo!("<reason>")`, with its test marked
`#[should_panic(expected = "<reason>")]` so the suite fails loudly until
the real code lands:

```rust
pub fn plan(&self) -> Result<Path, Error> {
    todo!("D* Lite: see docs/ROADMAP.md")
}
```

```rust
#[test]
#[should_panic(expected = "D* Lite")]
fn plan_is_not_implemented_yet() {}
```

The expected string does the work pytest's `strict=True` does: it names
what is blocking the implementation, and it forces the test to change in
the same commit that lands the real code. Since `clippy::todo` sits at
`warn` in the baseline table and CI denies warnings, the stub also
carries an `#[expect(clippy::todo, reason = "...")]` repeating that same
blocker, which is what keeps a stub from going quiet. A bare `#[ignore]`
with no reason is the Rust form of the bare skip
[workflow/tdd.md](../workflow/tdd.md#no-silent-skips) prohibits.

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

## Crates called from another language

The section above covers a C ABI. A crate compiled as an extension module
for Python, Node, or Ruby is a different job, because the runtime on the
other side has a garbage collector and an interpreter lock to work
around. The examples below use PyO3; the principles hold for any of them.

- **The foreign API is the contract.** Argument names, positional order,
  default values, and raised error types are what the caller sees, and a
  change to any of them breaks that caller regardless of what the Rust
  signature looks like. Pin them with an explicit attribute
  (`#[pyo3(signature = (...))]`) rather than relying on the Rust signature
  to produce the right shape by accident, and test the foreign signature
  rather than the Rust one.
- **Release the interpreter lock around anything that computes.** Wrap the
  body in `py.allow_threads` so a caller can run several calls in
  parallel. A loop that calls back into the interpreter cannot do this,
  which is the reason for the next rule.
- **Injected callbacks become enums, never a bare foreign object.** A hot
  loop invoking a caller-supplied closure reacquires the lock on every
  iteration and gives back the entire speedup. Give the enum a native
  variant per built-in policy, plus one variant holding the foreign
  callable, and document that the foreign path is slow:

  ```rust
  enum Steerer {
      Dubins(DubinsSteerer),
      Straight(LineSteerer),
      Python(Py<PyAny>),
  }
  ```

- **Do not copy on the way in.** Borrow the caller's buffers
  (`PyReadonlyArray2`) rather than converting them. Copying on the way out
  is usually fine, since the result is smaller than the input and the
  caller owns it afterward.
- **Ship a stable-ABI artifact** (`abi3` for Python) so one wheel per
  platform covers every interpreter version above the floor, and state
  that floor in one place.
- **Ship type declarations**, a `.pyi` per module or the equivalent, so
  the caller's type checker and editor keep working against a compiled
  artifact.
- **A panic reaches the caller as something it cannot catch.** PyO3
  converts an unwind into an exception derived from `BaseException`, which
  an ordinary `except Exception` handler passes over and which tends to
  terminate the interpreter. A panic in a planner does not surface as a
  catchable error, it takes the process down, which is a harder argument
  against panicking constructs than a pure Rust library ever has.
- **Write doc comments for the foreign reader.** A comment on an exported
  item becomes that object's documentation in the calling language, so
  name arguments as that caller passes them and refer to that language's
  types rather than to the binding's wrapper types.

## Bounded resources

Applies to a crate at criticality C2 in
[workflow/criticality.md](../workflow/criticality.md). The reasoning
behind these rules, and the cross-language form of them, is in
[style/defensive.md](../style/defensive.md#bounded-resources).

`#![no_std]` stops `std` being linked and swaps the prelude. It does not
forbid allocation, because `alloc` is a separate opt-in. Those are two
decisions, and only the second is the one bounded memory needs.

The attribute is decorative unless CI proves it. A host build with a
`std` feature enabled will happily miss a leaked `std::` path, so the only
honest gate is a cross-compile against a bare-metal target, where no `std`
exists to leak:

```bash
cargo check --target thumbv7em-none-eabihf --no-default-features
```

`heapless` supplies fixed-capacity containers with capacity as a const
generic, turning a capacity failure into a `Result` instead of a
reallocation. Note what it does not do: **it removes the allocator, not
the panics.** `Vec::insert` returns a `Result` about capacity and still
panics on an out-of-range index; `remove`, `swap_remove`, `drain`, and the
`Extend` implementation all panic; and the containers dereference to
slices, so every panicking slice method stays reachable, including the
sort family, which may panic when the comparator is not a total order.
That last one fires whenever the sort key is a float, which is the normal
case for a cost. Pair fixed-capacity containers with `indexing_slicing`
from the hardened tier, and prefer `get` to bare indexing.

At a boundary that accepts variable-size input from outside, `try_reserve`
is stable and is the right tool. There is no stable `try_push`, and the
allocator API is still unstable.

**No recursion on a bounded path.** Replace tree and graph recursion with
an explicit stack of stated capacity, which gives the memory bound as well
as the acyclic call graph. No lint enforces this: `unconditional_recursion`
only catches a function that always recurses with no base case, and a
correct recursive search does not trip it. It is a review rule, and the
structural fix is what makes it checkable.

**Every search, sampling, or solving entry point takes a budget** and
returns a result that distinguishes convergence from budget exhaustion.
Do not offer an unlimited variant of the same function a real-time caller
uses.

A fixed capacity pays twice: it is also the unwind bound a bounded model
checker needs, so choosing the capacity makes the code verifiable and the
verification justifies the capacity.

## Proving a panic cannot happen

`panic = "abort"` removes unwinding, not panicking. The panic branches and
the formatting machinery stay in the binary, and only the behavior after a
panic changes. The lint tiers above keep panicking constructs out of your
own source, and say nothing about your dependencies.

Past that, in increasing order of strength and cost:

| Technique | What it proves | Cost |
|---|---|---|
| The hardened lint tier | This crate's own source contains no panicking construct | Free |
| Fixed-capacity containers | No allocator, so no out-of-memory path. Capacity failures are `Result` | API friction |
| `#[no_panic]` on selected entry points | Link-time proof across the whole dependency graph, for those functions only | Brittle |
| Bounded model checking with `kani` | Machine-checked absence of panics, overflow, and undefined behavior, up to a stated bound | Harness authoring, slow |

Three caveats decide how `#[no_panic]` is wired in, and all three surprise
people. Detection happens at link time, so a library crate's own
`cargo build` never triggers it and the crate needs a binary or an
integration-test target that links the annotated functions. It requires
optimization, so a debug build needs `opt-level = 1` and a release build
needs link-time optimization. And it does nothing under `panic = "abort"`,
so the proof runs in an unwind build while the shipped artifact still
aborts.

## Verification beyond the gate

The tools in [Required tooling](#required-tooling) are the blocking ones.
These are the scheduled ones, and they answer different questions.

| Tool | Catches | When |
|---|---|---|
| `proptest` | Input-space defects in pure functions. Commit the regression files | Per pull request |
| `cargo-mutants` | Tests that cannot fail, scoped to the diff | Per pull request |
| `cargo-careful` | The Miri family of defects, on real machine code, including through FFI | Per pull request, cheap |
| `kani` | Panics, overflow, and assertions over all inputs up to a bound | Scheduled |
| `cargo-fuzz` | Crashes from adversarial input, time-boxed | Scheduled |
| `loom` | Atomic ordering and interleavings | Only with hand-written shared state |

Four calibrations worth carrying, because each one contradicts a
reasonable assumption:

- **Miri never sees an FFI path.** It interprets the program and fails on
  a foreign call, so a test touching a C solver, a BLAS backend, or the
  binding layer is invisible to it. `cargo-careful` covers that gap.
- **Bounded model checking is a poor fit for floating-point code.** Kani's
  own documentation states that it returns a nondeterministic value in
  range for the transcendental functions, which makes it unsuitable for
  reasoning about numerical precision. Point it at integer and index
  logic: grid addressing, ring buffers, cost accumulation.
- **There is no branch-coverage gate.** `cargo-llvm-cov` gates on lines,
  regions, and functions; `--branch` needs nightly and has no
  `--fail-under` counterpart. Region coverage is the closest available
  proxy. Modified condition or decision coverage was removed from the
  compiler in 2025 and is not available at all.
- **This tooling buys specification clarity more than bug discovery.** The
  Rust Foundation reported that the standard library verification effort,
  across hundreds of proof harnesses, found no previously unknown
  memory-safety defects, only specification and documentation problems.
  Spend it on new unsafe code rather than on retrofitting reviewed code.

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
