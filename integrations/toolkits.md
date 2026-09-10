# Agent toolkits

A **base package** is a third-party agent toolkit a project installs
alongside this library: a spec-driven workflow, a skillset, a plugin that
changes how the agent behaves. Each one carries opinions about how work
should happen, and those opinions overlap with the ones written here.

This file records which base packages the family uses, and, wherever a
package and this library disagree, which of the two wins. Without that
arbitration an agent holds two contradictory instructions and picks by
accident.

## The packages

| Package | Role | Consumed as | Pinned at | Status |
| --- | --- | --- | --- | --- |
| [cc-sdd](https://github.com/gotalab/cc-sdd) | Spec-driven workflow, installed as 17 agent skills | `npx cc-sdd@<version>`, source vendored as a submodule | `v3.0.2` | Pilot, `clave` |
| [mattpocock-skills](https://github.com/mattpocock/skills) | Engineering skillset: TDD, review, design vocabulary, spec and ticket flows | Claude Code plugin | `v1.2.3` | Pilot, `clave` |
| [caveman](https://github.com/JuliusBrussee/caveman) | Output compression, on demand | Claude Code plugin | commit pin, no version declared | Pilot, `clave`, switched off |

**Pilot means pilot.** These are installed in one project. A project that
has not adopted them is not out of conformance, and nothing in this library
assumes they are present. Promote a package to family-wide only after a
full cycle of real work has run through it, and record that promotion here.

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

## Arbitration

| Overlap | This library says | The package says | Resolution |
| --- | --- | --- | --- |
| Where specs live | `docs/` | `.kiro/specs/` | The package. See [workflow/sdd.md](../workflow/sdd.md#where-specs-live) |
| Requirement format | EARS | EARS | No conflict, both require it |
| Whether a change may skip the spec | Spec-first is the minimum | `kiro-discovery` may route to direct implementation | This library. See [workflow/sdd.md](../workflow/sdd.md#gating-rule) |
| Who owns the spec pipeline | The project's spec documents | `kiro-*` skills, and separately the `to-spec` skill publishing to an issue tracker | cc-sdd owns it. Do not run `to-spec` in a project running cc-sdd |
| The TDD loop | Red, green, refactor | Same loop, run per task by `kiro-impl`, plus seams from the `tdd` skill | Both. The package adds, it does not contradict. See [workflow/tdd.md](../workflow/tdd.md#seams) |
| Reviewing a diff | Human merges, evidence scaled to blast radius | Two-axis review, standards and spec, in parallel subagents | Both. The skill is a mechanism, the merge rule is a policy. See [workflow/review.md](../workflow/review.md#running-a-review) |
| How agent instruction files are written | Thin bridges, one constitution | Context pointers, wording decides retrieval | Both. See [agents/claude.md](../agents/claude.md#pointer-wording) |
| Design vocabulary | Naming rules only | `codebase-design` fixes module, interface, depth, seam, adapter, leverage, locality | The package, inside design discussions. It does not override [style/naming.md](../style/naming.md) for identifiers |
| Prose voice | [agents/writing.md](../agents/writing.md) | caveman compresses to telegraphic fragments | This library, always, for anything committed. See [agents/writing.md](../agents/writing.md#compression-tools) |
| Where the project's domain vocabulary lives | `docs/` and the spec | A `CONTEXT.md` at the repository root | Either, per project. Pick one and point the other at it, rather than maintaining both |

## Adopting a package in a project

1. Install it, and record the exact pin in the project's own
   `standards/README.md` or equivalent. Read the pin from what the tool
   actually recorded, not from the tag you meant to install: a plugin whose
   manifest omits a `version` field pins to a commit instead.
2. Check its hooks before assuming it is inert. A plugin that registers a
   `SessionStart` or `UserPromptSubmit` hook is active from the moment it is
   installed, whatever its documentation implies.
3. Point the project's `CLAUDE.md` at this file rather than restating the
   arbitration locally.
4. If the package disagrees with this library in a way not listed above,
   add the row here. Do not settle it privately in one project, because the
   next project will hit the same disagreement and settle it differently.

## Adding a package to this list

A package earns a row after it has been used for real work, not after it
has been read about. Record what it is, how it is pinned, and every
overlap found while using it, including the ones resolved in its favor.
A list that only records wins for this library is a list nobody checked
honestly.
