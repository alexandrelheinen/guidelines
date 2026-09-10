# Keeping agent context lean

Agent-facing files (`AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`) get read at
the start of every session, by every agent, on every task: their cost is
paid constantly, not once. Keep them short enough that reading them is
cheap, and put the reasoning-heavy material somewhere it is loaded on
demand instead.

## What belongs where

| Belongs in the project | Belongs in this shared library |
|---|---|
| Project-specific setup (how to run the dev server, non-obvious environment quirks) | Naming, comments, and error-handling conventions that apply everywhere |
| The project's own spec documents (`docs/specification.md`, feature specs) | The SDD/TDD process itself |
| Domain-specific rules (e.g. an editorial voice for one specific site) | The general anti-AI-slop writing rules that voice builds on |
| One-off gotchas discovered while working in this codebase | Conventions stable enough to apply across projects |
| Which third-party toolkits the project installs, and their pins | Which of this library's rules those toolkits override, in [integrations/toolkits.md](../integrations/toolkits.md) |

If a rule would be identical if copied into every project's
`CONTRIBUTING.md`, it belongs here instead: write it once, link to it
everywhere (see [claude.md](claude.md#referencing-this-library)).

## Project history is not documentation

What happened to a project, meaning migrations, resets, abandoned
approaches, and dated status notes, is a project-management asset. It lives
in git, the changelog, and the issue tracker. Keeping it out of the README
and out of `docs/` is not tidiness, it is what stops documentation from
expiring: see
[writing.md](writing.md#documentation-is-timeless).

## Signs a file has grown too large

- An agent needs to read past the third section before finding the rule
  relevant to the current task.
- The same constraint is explained twice, once in general terms and once
  with a worked example "just in case."
- Environment-specific caveats (a particular sandbox's quirks) are mixed
  into rules that apply to every environment.

When any of these show up, split the file: keep the entry point thin, move
the detail to a linked document that is only loaded when the relevant task
comes up.

## Do not pre-empt hypothetical needs

Do not document a rule for a scenario the project has not encountered yet.
Add the rule when the scenario actually happens: a speculative rule adds
reading cost to every future session for a case that may never occur.
