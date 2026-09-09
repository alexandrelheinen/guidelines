# Rust

Thin: current usage is one early-stage crate, so this file leans on
standard Rust conventions to fill gaps rather than extracting a full house
style from a single stub project.

## Edition and safety

- `edition = "2021"` in `Cargo.toml`.
- `#![deny(unsafe_op_in_unsafe_fn)]` at the crate root: any `unsafe`
  block still has to justify its own invariants explicitly, this just
  closes the implicit-safety loophole for `unsafe fn` bodies.
- Any `unsafe` block requires a `// SAFETY:` comment immediately above it
  stating the invariant that makes it sound, and a test exercising that
  invariant where feasible.

## Formatting and linting

Use `rustfmt` defaults (no custom `rustfmt.toml` unless a specific project
need arises) and `clippy` with its default lint set at minimum:

```bash
cargo fmt --check
cargo clippy --all-targets -- -D warnings
```

## Error handling

Prefer `Result<T, E>` over panicking for any error a caller could
reasonably handle; reserve `panic!`/`assert!` for precondition violations
that indicate a bug in the calling code, not runtime conditions like
missing files or bad input. A public library crate should define its own
error enum rather than exposing `Box<dyn Error>` everywhere.

## Naming

Standard Rust conventions, consistent with
[style/naming.md](../style/naming.md): `snake_case` functions/variables/
modules, `PascalCase` types/traits, `SCREAMING_SNAKE_CASE` constants.

## Reference

[Rust API Guidelines](https://rust-lang.github.io/api-guidelines/): follow
its naming and error-handling recommendations for anything not covered
above, since local precedent here is still too thin to override them.
