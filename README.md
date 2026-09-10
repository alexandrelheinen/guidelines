# Guidelines

Centralized development guidelines shared across my personal projects, consumed
as a git submodule. Licensed under [CC BY 4.0](LICENSE); see
[CONTRIBUTING.md](CONTRIBUTING.md) for how this repository changes and how
to pin a project to a specific version, and [CHANGELOG.md](CHANGELOG.md)
for what changed in each release.

## What lives here

- **Agent instructions**: writing and behavior guidelines for AI coding agents
  (Claude, etc.), meant to be referenced from each project's `AGENTS.md`.
- **Development cycle guidelines**: Spec-Driven Development (SDD) and
  Test-Driven Development (TDD) workflow standards.
- **Language coding guidelines**: style and convention guides for Python,
  C++, C, Node.js, Rust, Ruby, and others as needed.
- **Issue and PR templates**: aligned with the SDD + TDD workflow.
- **Toolkit arbitration**: which third-party agent packages the family
  installs, and which of these guidelines each one overrides.

## Usage

Add this repo as a submodule tracking `main`:

```bash
git submodule add -b main https://github.com/alexandrelheinen/guidelines.git guidelines
```

Then reference the relevant files from your project's `AGENTS.md` /
`CLAUDE.md` instead of duplicating guidance locally.

Pull in the latest guideline changes later with:

```bash
git submodule update --remote --merge
```

A project that wants stability over freshness can pin the submodule to a
tagged release instead of tracking `main`. See
[CONTRIBUTING.md](CONTRIBUTING.md#consuming-a-specific-version) for how.

## Structure

Two bridge files sit at the repository root, one per agent-tool family:
`AGENTS.md` for Cursor, Copilot, and other generic tools, and `CLAUDE.md`
for Claude Code specifically. Both point into the folders below rather
than repeating their content.

The `agents/` folder holds guidance about how an agent should behave and
write. `writing.md` covers the prose rules that apply everywhere:
vocabulary, typography, and cadence. `article.md` adds structure rules
specific to long-form published content, building on `writing.md` rather
than repeating it. `claude.md` documents how a project should structure
its own agent-instruction files, plus the no-fabricated-evidence rule and
evidence-reporting tiers. `context.md` explains what belongs in this
shared library versus a single project's own files.

The `style/` folder holds cross-language conventions that belong to no
single language: `naming.md` for naming rules, `comments.md` for when and
how to document code, and `errors.md` for error-handling strategy.

The `workflow/` folder holds the development-cycle guidelines. `sdd.md`
and `tdd.md` define Spec-Driven Development and Test-Driven Development on
their own; `integration.md` explains how the two combine into a single
V-cycle. `branching.md`, `commits.md`, and `review.md` cover how work moves
from a branch to a merged pull request.

The `languages/` folder holds one file per language, named after its file
extension rather than its full name (`py.md`, `cpp.md`, `rs.md`, and so
on), so a project can point directly at the file matching the code it is
editing.

The `templates/` folder holds the issue and pull-request templates that
follow the SDD and TDD workflow above: bug and feature issue templates
under `templates/issue/`, a pull-request template, and a standalone spec
template for starting new work.

The `integrations/` folder holds `toolkits.md`, the record of which
third-party agent packages the family installs, how each is pinned, and
which of this library's rules a package overrides. A project installing a
skillset or a plugin points there instead of settling the same conflict
privately.

File names across the repository are one word, or a language extension, by
convention. See `style/naming.md` for how that convention itself is
defined.
