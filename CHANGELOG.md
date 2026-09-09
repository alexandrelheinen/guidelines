# Changelog

All notable changes to this repository are documented here. The format
follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versioning follows the scheme in
[CONTRIBUTING.md](CONTRIBUTING.md#versioning).

## [Unreleased]

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
