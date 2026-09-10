# Claude instructions

Entry point for Claude Code sessions working in this repository, and a
model for what a consuming project's own `CLAUDE.md` should reference from
this library once it is added as a submodule. The `@path` lines below are
Claude Code imports: opening this file loads each one automatically, so
treat them as part of this file rather than as optional links.

Three guidelines matter most and come first.

## 1. Method: how work gets done

@workflow/sdd.md
@workflow/integration.md
@workflow/tdd.md

Once work is ready to ship, the same treatment applies to
[workflow/branching.md](workflow/branching.md),
[workflow/commits.md](workflow/commits.md), and
[workflow/review.md](workflow/review.md), which cover branch naming,
commit format, and what a PR needs before a human merges it.

## 2. Writing: how any prose should read

@agents/writing.md
@agents/article.md

## 3. Naming

@style/naming.md

## Templates

Use these when opening an issue, starting a PR, or writing a spec, rather
than improvising a structure each time.

@templates/issue/bug.md
@templates/issue/feature.md
@templates/pr.md
@templates/spec.md

## Also read

These stay as plain links rather than imports, since a session consults
them for the task at hand rather than needing them every time:

- [style/comments.md](style/comments.md) and
  [style/errors.md](style/errors.md), the remaining cross-language style
  rules.
- `languages/<ext>.md` for whichever language the current task touches
  (`py.md`, `cpp.md`, `c.md`, `cmake.md`, `sh.md`, `rb.md`, `rs.md`,
  `js.md`, `ts.md`, `cs.md`).
- [agents/claude.md](agents/claude.md), how this library expects a
  consuming project to structure its own agent-instruction files, plus the
  no-fabricated-evidence rule and evidence-reporting tiers.
- [agents/context.md](agents/context.md), what belongs in this shared
  library versus in a single project's own `CONTRIBUTING.md`.
- [integrations/toolkits.md](integrations/toolkits.md), read this whenever
  an installed skill or plugin tells you to do something these guidelines
  forbid, or forbids something they require. It records which of the two
  wins for every known conflict, so the answer is looked up rather than
  guessed.

For what this repository is and how a project consumes it as a submodule,
read [README.md](README.md). [AGENTS.md](AGENTS.md) covers the same ground
for non-Claude agent tooling (Cursor, Copilot), using plain links only,
since those tools do not resolve Claude Code's import syntax.
