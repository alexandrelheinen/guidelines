# Structuring agent instruction files

How a project's `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/*`, and
`.github/copilot-instructions.md` should relate to each other and to this
library.

## Thin bridges, one constitution

Every agent-entry-point file (`AGENTS.md`, `CLAUDE.md`, Cursor rules,
Copilot instructions) is a pointer, never a place to write or duplicate
actual rules. Each project has exactly one "constitution" —
`CONTRIBUTING.md` (or `docs/guidelines.md`) — that all of them point to.

```markdown
# Agent instructions

This file is a bridge only. **Do not add rules here.**

Read [CONTRIBUTING.md](CONTRIBUTING.md) for development lifecycle,
engineering standards, and AI agent rules.
```

Why: five copies of the same rule drift out of sync the first time one of
them gets edited in isolation. One file, several pointers, keeps every
agent and every human reading the same source.

Conflict resolution order when instructions disagree: direct maintainer
request > the project's `CONTRIBUTING.md` > this shared library > general
best practices for the language/framework.

## Referencing this library

A consuming project's `CONTRIBUTING.md` cites specific files here instead
of restating their content:

```markdown
Naming conventions: see guidelines/style/naming.md.
Commit format: see guidelines/workflow/commits.md.
```

Don't copy paragraphs from this library into a project's own files — link
to them, so an update here doesn't require updating every consumer by hand.

## No fabricated evidence

Do not present fabricated, staged, or edited output as real. This applies
to test results, benchmark numbers, screenshots, and demo recordings alike.

Forbidden: post-processed overlays on captured output, composited
"before/after" images that add visuals not actually produced by the system,
decorative output presented as real feedback, scripts that synthesize data
when the real data source is empty, committing generated assets without
verifying their provenance.

Allowed: real output driven by actual execution, capture scripts that only
*record* what the system produced, clearly labeled placeholders for
incomplete work, and documented limitations.

When asked to solve a problem: fix the root cause, or state plainly that
you can't yet and what's blocking you. "Looks correct in the screenshot"
without the underlying behavior being correct is a failed task, not a
workaround.

## Reporting evidence, scaled to blast radius

Scale the evidence attached to a PR or status update to the size and risk
of the change, not a fixed template:

| Tier | Change | Evidence expected |
|---|---|---|
| T0 | Typo, doc-only | None beyond the diff |
| T1 | Small internal change, low risk | Test output for the touched area |
| T2 | New feature, moderate surface | Full local validation run + relevant test output |
| T3 | Public API, architecture, or hot-path change | Full validation run + before/after comparison where applicable |
| T4 | Hardware, safety-critical, or irreversible-in-production change | All of the above, plus explicit sign-off request before merge |

Separate the timeline (what you actually ran, in order) from the analysis
(what you concluded from it) in any report — conflating the two is how
optimistic interpretation quietly replaces evidence.

## Cloud/sandboxed agents

If an agent runs somewhere without access to project-specific hardware or
services (e.g. no physical device, no production credentials), it must not
claim that class of validation happened. State explicitly what was and
wasn't verified, and document what a human needs to check manually before
merge.
