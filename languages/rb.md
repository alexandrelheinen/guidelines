# Ruby

Thin — Ruby usage so far is limited to one Jekyll static-site project, so
this file documents what's actually in place rather than a full house
style. Expand it as Ruby usage grows.

## Toolchain

Ruby version pinned via `mise` (or an equivalent version manager) rather
than relying on system Ruby, since distro-packaged Ruby versions often
lag behind what a `Gemfile` requires. Pin the exact version in a
`.mise.toml`/`.ruby-version` file, not just in the `Gemfile` constraint.

## Conventions

No dedicated linter (Rubocop or similar) is configured yet on the one
project using Ruby. Until one is adopted:

- Match the existing file's style exactly rather than introducing a new
  convention mid-file.
- `snake_case` for methods, local variables, and file names; `PascalCase`
  for classes and modules; `SCREAMING_SNAKE_CASE` for constants — the
  Ruby-community default, consistent with [style/naming.md](../style/naming.md).

## Adopting Rubocop

If a project's Ruby surface grows enough to warrant linting, adopt
Rubocop with its default cop set as the starting point, then document any
project-specific overrides in that project's own guideline file (not here,
until the override is genuinely shared across more than one project).
