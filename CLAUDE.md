# Claude instructions

Entry point for Claude Code sessions working in this repository, and a
model for what a consuming project's own `CLAUDE.md` should reference from
this library once it is added as a submodule.

Three guidelines matter most and come first. Read them before writing any
code, prose, or spec:

## 1. Method: how work gets done

- [workflow/sdd.md](workflow/sdd.md), how a spec is written before
  implementation starts.
- [workflow/integration.md](workflow/integration.md), how the spec, the
  V-cycle, and TDD combine into one loop per task.
- [workflow/tdd.md](workflow/tdd.md), the red, green, refactor cycle and
  coverage expectations.

Once work is ready to ship: [workflow/branching.md](workflow/branching.md),
[workflow/commits.md](workflow/commits.md), and
[workflow/review.md](workflow/review.md) cover branch naming, commit
format, and what a PR needs before a human merges it.

## 2. Writing: how any prose should read

- [agents/writing.md](agents/writing.md), the base rules for all prose:
  vocabulary, typography, cadence, and the patterns that read as
  AI-generated regardless of topic. Applies to commit bodies, PR
  descriptions, and documentation, not only published content.
- [agents/article.md](agents/article.md), structure rules specific to
  long-form articles and posts, building on writing.md rather than
  repeating it.

## 3. Naming

- [style/naming.md](style/naming.md), the cross-language naming rules:
  physical-variable naming, casing by construct, file naming, test naming.

## Templates

Use these when opening an issue, starting a PR, or writing a spec, rather
than improvising a structure each time:

- [templates/issue/bug.md](templates/issue/bug.md)
- [templates/issue/feature.md](templates/issue/feature.md)
- [templates/pr.md](templates/pr.md)
- [templates/spec.md](templates/spec.md)

## Also read

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

For what this repository is and how a project consumes it as a submodule,
read [README.md](README.md). [AGENTS.md](AGENTS.md) covers the same ground
for non-Claude agent tooling (Cursor, Copilot).
