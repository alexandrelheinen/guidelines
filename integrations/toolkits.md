# Agent toolkits

A **base package** is a third-party agent toolkit a project installs
alongside this library: a spec-driven workflow, a skillset, a plugin that
changes how the agent behaves. Each one carries opinions about how work
should happen, and those opinions overlap with the ones written here.

This file records which base packages the family has tried, how to choose
among SDD approaches, and, wherever a package and this library disagree,
which of the two wins. Without that arbitration an agent holds two
contradictory instructions and picks by accident.

## Choosing an SDD approach

Hand-written specs under `docs/` are the family default. A third-party
SDD suite is optional. Pick one that fits the project; do not install one
because another project did.

| Approach | Fit | Avoid when |
| --- | --- | --- |
| Hand `docs/` SDD | Default for every project. Fits brownfield work and repos that already document in `docs/` | — |
| [OpenSpec](https://github.com/Fission-AI/OpenSpec) | A steady stream of changes that benefit from deltas plus an archive of decided specs | The project wants stage-gated Kiro-style ceremony |
| [cc-sdd](https://github.com/gotalab/cc-sdd) | The project wants Kiro-shaped artifacts and `kiro-impl` outside the Kiro IDE | Small projects, Cursor-first apps, cost-sensitive work, or brownfield repos that already live in `docs/` |
| [Spec Kit](https://github.com/github/spec-kit) | Greenfield work or constitution-heavy features that benefit from a formal kit layout | Small SPA fixes and other low-ceremony changes |

OpenSpec and Spec Kit have no family pilot row yet. A project that adopts
either records its own pin and any arbitration locally until a second
pilot justifies a row here. Do not treat either as family-wide guidance.

## The packages

| Package | Role | Consumed as | Pinned at | Status |
| --- | --- | --- | --- | --- |
| [cc-sdd](https://github.com/gotalab/cc-sdd) | Spec-driven workflow, installed as 17 agent skills | `npx cc-sdd@<version>`, source vendored as a submodule | `v3.0.2` | Pilot in `clave` only. Not the family default. Not recommended as the default for brownfield or Cursor-first apps unless a later ADR promotes it |
| [mattpocock-skills](https://github.com/mattpocock/skills) | Engineering skillset: TDD, review, design vocabulary, spec and ticket flows | Claude Code plugin | `v1.2.3` | Pilot in `clave` only. Not family-wide |
| [caveman](https://github.com/JuliusBrussee/caveman) | Output compression, on demand | Claude Code plugin | commit pin, no version declared | Pilot in `clave`, switched off |
| Official library skills (for example FastAPI via [library-skills](https://github.com/tiangolo/library-skills)) | Stack idioms that consume the project's standards | Project skill install under `.agents/skills` (or `.claude/skills`), version-synced with the dependency | Exact package version the project pinned | Prefer these over unofficial tip packs when a maintained official skill exists |

**Pilot means pilot.** These are installed in one project. A project that
has not adopted them is not out of conformance, and nothing in this library
assumes they are present. Promote a package to family-wide only after a
full cycle of real work has run through it in more than one project, and
record that promotion here.

A project may combine hand `docs/` SDD with project-local workflow commands
and version-pinned stack skills. That layout is conforming. It does not
require cc-sdd or `.kiro/`.

## The governing principle

Sort every skill into one of two kinds, because the two need opposite
treatment.

A skill that **consumes** the standard reads what the project already
documents and applies it. The `code-review` skill is the model: it reviews
a diff against "this repo's documented coding standards", which means it
reads `languages/rs.md` and enforces it. Skills of this kind need no
arbitration and are worth preferring when choosing between two that do the
same job.

A skill that **asserts** a standard carries its own opinion about naming,
layout, process, or voice, and applies it whether or not the project
agrees. These need a decision, recorded below, and sometimes need switching
off. The decision is not about quality: an asserting skill can be very good
and still lose, because a standard that bends to whatever was installed
last is not a standard.

This library itself stays standards and process prose. Do not convert it
into a tree of asserting skills. How the three layers (standards, stack
skills, workflow commands) fit together is in
[agents/skills.md](../agents/skills.md).

## Arbitration

Rows that mention `.kiro/` or `kiro-*` apply only when the project has
adopted cc-sdd. Every other project keeps `docs/` as the spec home and
owns its own pipeline.

| Overlap | This library says | The package says | Resolution |
| --- | --- | --- | --- |
| Where specs live | `docs/` by default | `.kiro/specs/` when cc-sdd is installed | The project's chosen layout. `docs/` unless the project adopted cc-sdd, in which case `.kiro/specs/` is where that toolkit reads. See [workflow/sdd.md](../workflow/sdd.md#where-specs-live) |
| Requirement format | EARS | EARS | No conflict, both require it |
| Whether a change may skip the spec | The full cycle applies to architecture, usage changes, new features, and new modules. A small obvious fix skips it when the cause is known; an uncertain cause gets a short contract in the issue | `kiro-discovery` may route to direct implementation | This library. A human applies [workflow/sdd.md](../workflow/sdd.md#applicability). A skill may not waive a gate. See [the gating rule](../workflow/sdd.md#gating-rule) |
| Who owns the spec pipeline | The project's chosen layout (`docs/` by default) | `kiro-*` skills when cc-sdd is adopted; separately the `to-spec` skill publishing to an issue tracker | The project. When cc-sdd is adopted, those skills own the `.kiro/` pipeline for that project. Do not run `to-spec` alongside a `docs/` SDD publisher or alongside cc-sdd |
| The TDD loop | Red, green, refactor | Same loop, run per task by `kiro-impl` when cc-sdd is adopted, plus seams from the `tdd` skill when mattpocock-skills is installed | Both where installed. The package adds, it does not contradict. See [workflow/tdd.md](../workflow/tdd.md#seams) |
| Reviewing a diff | Human merges, evidence scaled to blast radius | Two-axis review, standards and spec, in parallel subagents | Both. The skill is a mechanism, the merge rule is a policy. See [workflow/review.md](../workflow/review.md#running-a-review) |
| How agent instruction files are written | Project rules live in the README and `docs/`. `CONTRIBUTING.md` is the process constitution. `CLAUDE.md` holds short verifiable agent behavior and enforces nothing | Context pointers, wording decides retrieval | Both. See [agents/claude.md](../agents/claude.md#where-rules-live) |
| Design vocabulary | Naming rules only | `codebase-design` fixes module, interface, depth, seam, adapter, leverage, locality | The package, inside design discussions. It does not override [style/naming.md](../style/naming.md) for identifiers |
| Prose voice | [agents/writing.md](../agents/writing.md) | caveman compresses to telegraphic fragments | This library, always, for anything committed. See [agents/writing.md](../agents/writing.md#compression-tools) |
| Where the project's domain vocabulary lives | `docs/` and the spec | A `CONTEXT.md` at the repository root | Either, per project. Pick one and point the other at it, rather than maintaining both |
| Official stack skill vs `languages/*.md` | Family law in the language file | Framework idioms in the official skill | The language file wins for family law. The skill wins for framework idioms unless a local arbitration note says otherwise |

## Adopting a package in a project

1. Install it, and record the exact pin in the project's own
   `standards/README.md`, `.agents/skills` lock, or equivalent. Read the
   pin from what the tool actually recorded, not from the tag you meant to
   install: a plugin whose manifest omits a `version` field pins to a
   commit instead.
2. Check its hooks before assuming it is inert. A plugin that registers a
   `SessionStart` or `UserPromptSubmit` hook is active from the moment it is
   installed, whatever its documentation implies.
3. Point the project's `CLAUDE.md` / `AGENTS.md` at this file rather than
   restating the arbitration locally. When an upstream skill asserts advice
   that conflicts with this library or with project docs, add a local
   arbitration note next to the pin.
4. If the package disagrees with this library in a way not listed above,
   add the row here. Do not settle it privately in one project, because the
   next project will hit the same disagreement and settle it differently.

## Adding a package to this list

A package earns a row after it has been used for real work, not after it
has been read about. Record what it is, how it is pinned, and every
overlap found while using it, including the ones resolved in its favor.
A list that only records wins for this library is a list nobody checked
honestly.
