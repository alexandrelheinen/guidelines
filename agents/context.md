# Keeping agent context lean

Agent-facing files (`AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`) get read at
the start of every session, by every agent, on every task — their cost is
paid constantly, not once. Keep them short enough that reading them is
cheap, and put the reasoning-heavy material somewhere it's loaded on
demand instead.

## What belongs where

| Belongs in the project | Belongs in this shared library |
|---|---|
| Project-specific setup (how to run the dev server, non-obvious environment quirks) | Naming, comments, and error-handling conventions that apply everywhere |
| The project's own spec documents (`docs/specification.md`, feature specs) | The SDD/TDD process itself |
| Domain-specific rules (e.g. an editorial voice for one specific site) | The general anti-AI-slop writing rules that voice builds on |
| One-off gotchas discovered while working in this codebase | Conventions stable enough to apply across projects |

If a rule would be identical if copied into every project's
`CONTRIBUTING.md`, it belongs here instead — write it once, link to it
everywhere (see [claude.md](claude.md#referencing-this-library)).

## Signs a file has grown too large

- An agent needs to read past the third section before finding the rule
  relevant to the current task.
- The same constraint is explained twice, once in general terms and once
  with a worked example "just in case."
- Environment-specific caveats (a particular sandbox's quirks) are mixed
  into rules that apply to every environment.

When any of these show up, split the file: keep the entry point thin, move
the detail to a linked document that's only loaded when the relevant task
comes up.

## Don't pre-empt hypothetical needs

Don't document a rule for a scenario the project hasn't encountered yet.
Add the rule when the scenario actually happens — a speculative rule adds
reading cost to every future session for a case that may never occur.
