# Agent skills and workflow commands

How installed skills relate to this library, and how a project keeps them
from rewriting house standards by accident.

## Three layers

Treat agent guidance as three layers with different jobs:

| Layer | What it holds | Where it lives |
| --- | --- | --- |
| 1. Standards | Naming, prose, SDD/TDD, language law, project rules | This library, plus the project's README and `docs/` |
| 2. Stack skills | Framework idioms that **consume** those standards | Official, version-synced skills (for example via [library-skills](https://github.com/tiangolo/library-skills)), pinned in the repo |
| 3. Workflow commands | Project-local slash commands and short operating steps | The project's own command or skill files, not this library |

Layer 1 decides family law. Layer 2 teaches how to use a stack without
replacing layer 1. Layer 3 encodes how *this* project runs its loop.

Do not convert this library into a tree of asserting skills. A consuming
project links here; it does not re-home the standards as skill prose that
competes with `languages/*.md` or `workflow/*.md`.

## Prefer official, version-synced skills

When a maintained official skill exists for a dependency the project
uses, prefer it over an unofficial tip pack. Official skills that ship
with the library, and installers such as `library-skills` that symlink
into `.agents/skills` (or `.claude/skills` for Claude Code), stay aligned
with the installed package version.

Record an **in-repo pin** for every installed skill: the package version,
commit, or lock entry the installer actually wrote. Prefer committing the
project-local skill path or symlink so a clone reproduces the same
guidance. When an upstream skill asserts advice that conflicts with this
library or with project docs, write a short local arbitration note next
to that pin. Do not leave the conflict for the agent to guess.

Conflict defaults, unless a local note says otherwise:

- `languages/*.md` and the rest of this library win for family law.
- The official stack skill wins for framework idioms.
- Known overlaps that need a family-wide decision go in
  [integrations/toolkits.md](../integrations/toolkits.md#arbitration).

## Workflow commands stay in the project

Project-specific commands (for example a `/project-*` flow that opens a
spec, runs validation, or prepares a PR) belong in the consuming
repository. This library does not ship those commands, and a project
must not copy another project's command text here in order to "share"
it. Share process by linking to `workflow/*.md`; keep the invocation
local.

## Cursor and agent-neutral paths

Many agents read skills from `.agents/skills`. Claude Code commonly uses
`.claude/skills`. Either path is fine; pick what the tool supports, pin
what you install, and point `AGENTS.md` / `CLAUDE.md` at
[integrations/toolkits.md](../integrations/toolkits.md) for arbitration
rather than restating it. Thin bridges stay thin: see
[claude.md](claude.md#thin-bridges).
