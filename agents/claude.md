# Structuring agent instruction files

How a project's `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/*`, and
`.github/copilot-instructions.md` should relate to each other and to this
library.

## Thin bridges, one constitution

Every agent-entry-point file (`AGENTS.md`, `CLAUDE.md`, Cursor rules,
Copilot instructions) is a pointer, never a place to write or duplicate
actual rules. Each project has exactly one "constitution," `CONTRIBUTING.md`
(or `docs/guidelines.md`), that all of them point to.

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
request > the project's `CONTRIBUTING.md` > this shared library >
third-party skills and plugins the project installs > general best
practices for the language or framework.

Installed skillsets sit below this library on purpose. A skillset is
somebody else's opinion about how to work, often a good one, and it is
welcome as a mechanism; it does not get to redefine the house voice,
naming, or process by being installed. Where a package and this library
genuinely conflict, the decision is recorded once in
[integrations/toolkits.md](../integrations/toolkits.md#arbitration) rather
than settled again in every project.

## Referencing this library

Two mechanisms serve two different audiences.

For Claude Code specifically, a project's `CLAUDE.md` can use Claude
Code's import syntax to load a guideline file's content directly. A line
containing only `@guidelines/style/naming.md` pulls that file into context
the moment `CLAUDE.md` is read, instead of requiring a follow-up read
later in the session:

```markdown
## Naming

@guidelines/style/naming.md
```

Reserve imports for the files a session needs on every task, typically the
method files (`workflow/sdd.md`, `workflow/integration.md`,
`workflow/tdd.md`), the writing files, and naming. See this library's own
[CLAUDE.md](../CLAUDE.md) for a worked example.

For any other tool, or for a file consulted only for specific tasks, a
plain link is enough:

```markdown
Naming conventions: see guidelines/style/naming.md.
Commit format: see guidelines/workflow/commits.md.
```

`AGENTS.md` should always use plain links rather than imports, since
Cursor, Copilot, and other tools that read `AGENTS.md` do not resolve
Claude Code's import syntax.

Either way, do not copy paragraphs from this library into a project's own
files. Link or import instead, so an update here does not require updating
every consumer by hand.

## Pointer wording

A line in `AGENTS.md` naming a document, and a skill's `description` field,
are the same object: a pointer held in context that names material sitting
outside it, plus the condition for going to fetch it. How that pointer is
worded, and not what it points at, decides whether the agent actually
reaches the material.

So a rule that matters and is being missed is usually a wording problem
before it is a placement problem. Sharpen the condition first, naming the
situation that should trigger the read in the words the task will actually
use, and only inline the material if sharpening fails. Inlining is the
expensive fix: it costs context on every session, including the ones where
the rule does not apply.

## Declaring the project's toolkit

A project that installs third-party skills or plugins says so in one place,
with the exact pin each tool recorded, and points at
[integrations/toolkits.md](../integrations/toolkits.md) for the arbitration
rather than restating it. Read the pin from what the installer wrote, not
from the tag you meant to install, and check whether the package registers
session hooks before describing it as inactive.

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
you cannot yet and what is blocking you. "Looks correct in the screenshot"
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
(what you concluded from it) in any report: conflating the two is how
optimistic interpretation quietly replaces evidence.

## Cloud/sandboxed agents

If an agent runs somewhere without access to project-specific hardware or
services (e.g. no physical device, no production credentials), it must not
claim that class of validation happened. State explicitly what was and
was not verified, and document what a human needs to check manually before
merge.
