# Changelog

All notable changes to this repository are documented here. The format
follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versioning follows the scheme in
[CONTRIBUTING.md](CONTRIBUTING.md#versioning).

## [Unreleased]

### Added

- `languages/rs.md`: a project layout section, covering the split of a
  workspace into crates along the dependency layering it wants enforced
  and keeping a library crate free of a binary's dependencies; acronym
  and trait naming, and the file naming a port may deviate from; the
  rustdoc spelling of the Google-style docstring sections as a mapping
  table with a worked example; test naming; and a section on crates
  compiled as an extension module for another language, covering the
  foreign signature as the contract, releasing the interpreter lock,
  injected callbacks as enums, borrowing the caller's buffers, stable-ABI
  artifacts, and shipping type declarations.
- `languages/rs.md`: a per-crate error enum in a workspace, every one of
  them converting into a single root error, so a caller matches on one
  type without the leaf crates depending on each other.

### Changed

- `workflow/tdd.md`: the Rust entry for marking intentionally
  unimplemented work now pairs `todo!("<reason>")` with
  `#[should_panic(expected = "<reason>")]`, so the marker names what is
  blocking the work and has to change in the commit that lands the
  implementation. Test layout notes that Rust's unit/integration split is
  forced by visibility rather than chosen.
- `style/naming.md`: test names drop the `test_` prefix in a framework
  whose attribute already marks the function.
- `style/comments.md`: the Rust entry names the rustdoc headings instead
  of deferring to the C++ and Python shapes.

## [1.2.0] - 2026-09-10

### Added

- `integrations/toolkits.md`: the record of which third-party agent
  packages the family installs (cc-sdd, mattpocock-skills, caveman), how
  each is pinned, and which of these guidelines each one overrides. It
  sorts skills into those that consume the project's standard and those
  that assert a competing one, arbitrates every known conflict in a table,
  and sets the rule that a package is promoted beyond a pilot project only
  after real work has run through it. All three are currently pilots in
  `clave` alone; no other project is out of conformance for not having
  them.
- `agents/writing.md`: a "Documentation is timeless" section. A README or a
  document under `docs/` describes what the thing is, in the present tense,
  and does not narrate what happened to the project. Dated status notes,
  "recently", and accounts of what the code used to be belong to git, the
  changelog, and the issue tracker. A claim about something outside the
  project may still carry a date, since that is what tells a reader when to
  recheck it. The self-review checklist gained a matching item.
- `agents/writing.md`: a "Compression tools" section scoping caveman and
  anything like it to throwaway output, never to committed prose, with a
  warning that a plugin registering a `SessionStart` hook is active from
  installation whatever its documentation implies.
- `agents/claude.md`: a "Pointer wording" section, that a missed rule is
  usually a badly worded pointer before it is a placement problem, and a
  "Declaring the project's toolkit" section on recording the pin the
  installer actually wrote rather than the tag that was meant.
- `agents/context.md`: a "Project history is not documentation" section and
  a table row separating a project's toolkit pins from the arbitration that
  belongs in this library.
- `workflow/tdd.md`: a "Seams" section, test at the public boundary and
  agree the boundaries before writing the tests, and a "Running the loop
  per task" section for when a toolkit drives red-green-refactor with the
  reviewer role separated from the implementer.
- `workflow/review.md`: a "Running a review" section splitting review into
  a standards axis and a spec axis, run separately so a tidy diff cannot
  talk a reviewer out of a missing requirement.
- `workflow/integration.md`: a mapping from the six V-cycle steps to the
  cc-sdd phases, and the note that verification and merge are the two steps
  a toolkit does not get to redefine.

### Changed

- `workflow/sdd.md`: specs live in `.kiro/specs/` for a project running
  cc-sdd and in `docs/` for every other project, with `docs/decisions.md`
  staying put in both layouts. The gating rule now states that a toolkit
  offering to skip the spec is making a suggestion rather than granting
  permission: a human may waive the spec, a skill may not waive it on the
  human's behalf.
- `agents/claude.md`: the conflict resolution order now names installed
  skills and plugins explicitly, below this library and above general
  language best practices.
- `templates/spec.md`: notes that it serves projects writing specs by hand
  under `docs/`, and becomes a content checklist for a project whose specs
  are generated by cc-sdd.
- `languages/cmake.md`: dropped "currently" from the formatter note, per
  the new timeless-documentation rule.
- `README.md`, `AGENTS.md`, `CLAUDE.md`: point at `integrations/toolkits.md`.

## [1.1.0] - 2026-09-10

### Changed

- `languages/rs.md`: replaced the placeholder file with a full Rust
  guideline. It sets edition 2024 and a pinned toolchain, a stable-only
  `rustfmt.toml` at 100 columns, lint tiers configured through the
  `[workspace.lints]` table (a baseline for every crate and a hardened
  restriction set for real-time, `unsafe`, and FFI crates), an unsafe-code
  policy built on `SAFETY` comments and Miri, panic and arithmetic rules,
  `thiserror` error enums over `Box<dyn Error>`, rustdoc conventions,
  dependency and supply-chain gates, FFI rules, and the required tool
  list with the commands a project's validate script runs. The rules
  derive from the Safety-Critical Rust Coding Guidelines and the Linux
  kernel Rust coding guidelines, both cited inline where a rule maps to
  one of theirs.
- `workflow/tdd.md`: added the Rust form of marking intentionally
  unimplemented work (`todo!()` plus a `#[should_panic]` test), alongside
  the existing Python and C++ entries.

## [1.0.0] - 2026-09-09

### Added

- `style/naming.md`, `style/comments.md`, `style/errors.md`: cross-language
  naming, documentation, and error-handling conventions.
- `workflow/sdd.md`, `workflow/tdd.md`, `workflow/integration.md`,
  `workflow/branching.md`, `workflow/commits.md`, `workflow/review.md`: the
  Spec-Driven Development and Test-Driven Development process, and how
  work moves from a branch to a merged pull request.
- `agents/writing.md`, `agents/article.md`: prose rules and long-form
  article structure, grounded in Orwell's writing rules, plain-English
  standards, and published research on AI-generated text.
- `agents/claude.md`, `agents/context.md`: how a project should structure
  its own agent-instruction files, the no-fabricated-evidence rule, and
  what belongs in this library versus a project's own files.
- `languages/py.md`, `languages/cpp.md`, `languages/c.md`,
  `languages/cmake.md`, `languages/sh.md`, `languages/rb.md`,
  `languages/rs.md`, `languages/js.md`, `languages/ts.md`,
  `languages/cs.md`: per-language coding guidelines.
- `templates/issue/bug.md`, `templates/issue/feature.md`, `templates/pr.md`,
  `templates/spec.md`: issue and pull-request templates aligned with the
  SDD and TDD workflow.
- Root `AGENTS.md` and `CLAUDE.md` bridge files, the latter using Claude
  Code's `@import` syntax for the highest-priority guidelines.
- `LICENSE` (CC BY 4.0), `CONTRIBUTING.md`, and this changelog.
- `.github/workflows/writing-lint.yml`: CI enforcement of the writing
  guideline on every push and pull request. Headings, titles, and table
  cells fail the build if they contain an em dash; the whole repository
  fails on an en dash, a curly quote, the ellipsis character, or an
  English contraction.
- `.gitattributes`: forces LF line endings for every text file,
  regardless of a contributor's local Git configuration.
- README: the submodule update command
  (`git submodule update --remote --merge`) and a pointer to pinning a
  specific version instead of tracking `main`.

### Changed

- `languages/cpp.md`: resolved the C++ style conflict between projects.
  The `.clang-format` shared by `bossa` and `fret` is now the settled
  house style; a project shipping different parameters should migrate to
  it, not keep it as a second accepted style.
- `agents/writing.md`: resolved the conflict with `website`'s established
  editorial voice. An occasional em dash is now allowed in body prose;
  headings, titles, and table cells still never take one, and en dashes,
  curly quotes, the ellipsis character, and English contractions remain
  banned everywhere.
