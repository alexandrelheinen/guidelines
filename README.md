# Guidelines

Centralized development guidelines shared across my personal projects, consumed
as a git submodule.

## What lives here

- **Agent instructions** — writing and behavior guidelines for AI coding agents
  (Claude, etc.), meant to be referenced from each project's `AGENTS.md`.
- **Development cycle guidelines** — Spec-Driven Development (SDD) and
  Test-Driven Development (TDD) workflow standards.
- **Language coding guidelines** — style and convention guides for Python,
  C++, C, Node.js, Rust, Ruby, and others as needed.
- **Issue and PR templates** — aligned with the SDD + TDD workflow.

## Usage

Add this repo as a submodule tracking `main`:

```bash
git submodule add -b main https://github.com/alexandrelheinen/guidelines.git guidelines
```

Then reference the relevant files from your project's `AGENTS.md` /
`CLAUDE.md` instead of duplicating guidance locally.

## Structure

```
guidelines/
├── agents/       # AI agent writing & behavior guidelines
├── workflow/     # SDD / TDD process guidelines
├── languages/    # per-language coding guidelines
└── templates/    # issue & PR templates
```

(Directories are added as content is migrated in from individual projects.)
