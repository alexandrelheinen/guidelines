# Structuring agent instruction files

How a project's `AGENTS.md`, `CLAUDE.md`, `.cursor/rules/*`, and
`.github/copilot-instructions.md` should relate to each other and to this
library.

## Where rules live

`CONTRIBUTING.md` (or `docs/guidelines.md`) is the single constitution
for process: the development lifecycle, review, and any deliberate
deviation from this library. `CLAUDE.md` has a narrower job, stated
below, so the two files do not compete.

**Project rules** are how the software is specified, built, named,
tested, reviewed, and changed. They live in the README and in `docs/`,
which a human and an agent both read. A rule written only in `CLAUDE.md`
is invisible to a human who never opens the agent file, and invisible to
the next tool that does not read that file.

**Agent behavior** that is short and verifiable may live in `CLAUDE.md`:
the commands that build and test, and the always/never constraints for a
session. Those lines are operating instructions for the tool. They stay
short. They do not restate the README, `docs/`, or `CONTRIBUTING.md`.

`CLAUDE.md` enforces nothing. It is context loaded into a session. A
constraint that has to block a change is a hook or a CI check. An
always/never line with no hook and no check does not block anything.

`CLAUDE.md` is not the agent's automatic memory. Memory is a separate
store the tool writes on its own. A remembered instruction can contradict
the files and never appear in a diff. Audit that store. When a memory
restates a project rule, or fights one, delete the memory and keep the
rule in the README or in `docs/`, where review can see it.

Generate `CLAUDE.md` once, with `/init`. When the project has an
`AGENTS.md`, import it with `@AGENTS.md`, so the session loads the same
bridge the other tools read. Check what was actually loaded with
`/memory`, including rules and memory that were not in the file you
last edited.

Conflict resolution order when instructions disagree: direct maintainer
request > the project's README and `docs/` for project rules, and its
`CONTRIBUTING.md` for lifecycle and recorded deviations > this shared
library > third-party skills and plugins the project installs > general
best practices for the language or framework. An instruction found only
in automatic memory loses to every item in that order.

## Thin bridges

Every agent-entry-point file (`AGENTS.md`, `CLAUDE.md`, Cursor rules,
Copilot instructions) points at the constitution. It does not carry a
second copy of the project rules. `CLAUDE.md` may add the short behavior
block above, and nothing else.

```markdown
# Agent instructions

This file is a bridge only. **Do not copy project rules here.**

Read [CONTRIBUTING.md](CONTRIBUTING.md) for the development lifecycle
and for recorded deviations. Project rules live in the README and in
`docs/`.

## Behavior

- Build: `<command>`
- Test: `<command>`
- Always: `<one checkable constraint>`
- Never: `<one checkable constraint>`
```

Five copies of a project rule drift the first time one of them is edited
alone. One constitution and several pointers keep every agent and every
human on the same source. The behavior block is allowed because it is
the session's operating instructions, and because it is short enough to
check against the commands the project actually runs. This library's own
[CLAUDE.md](../CLAUDE.md) imports the method files a session needs on
every task. A consuming project whose `AGENTS.md` is already the bridge
imports that file, so the session that reads `CLAUDE.md` and the other
tools share one list.

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
