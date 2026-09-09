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
├── AGENTS.md               # bridge — read this first if you're an agent
├── agents/                 # AI agent writing & behavior guidelines
│   ├── writing.md          #   editorial rules / anti-AI-slop
│   ├── claude.md           #   thin-bridge pattern, no-fabricated-evidence rule
│   └── context.md          #   what belongs here vs. in a consuming project
├── style/                  # cross-language conventions
│   ├── naming.md
│   ├── comments.md
│   └── errors.md
├── workflow/                # SDD / TDD process guidelines
│   ├── sdd.md
│   ├── tdd.md
│   ├── integration.md      #   how SDD + V-cycle + TDD combine
│   ├── branching.md
│   ├── commits.md
│   └── review.md
├── languages/               # per-language coding guidelines
│   ├── py.md      ├── cpp.md     ├── c.md
│   ├── cmake.md   ├── sh.md      ├── rb.md
│   ├── rs.md      ├── js.md      ├── ts.md
│   └── cs.md
└── templates/                # issue & PR templates
    ├── issue/
    │   ├── bug.md
    │   ├── feature.md
    │   └── config.yml
    ├── pr.md
    └── spec.md
```

File names are one word (or a language extension) by convention — see
`style/naming.md` for how that convention itself is defined.
