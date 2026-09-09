# Specification-Driven Development (SDD)

Write the spec before the implementation. The spec is the source of truth;
an issue or PR links to it rather than restating it.

## Required elements of a spec

Every spec, regardless of rigor level (below), states:

| Element | Content |
|---|---|
| Intent | What problem this solves and for whom |
| Scope | What is in and explicitly what is out |
| Acceptance criteria | Observable, testable conditions for "done" |
| Traceability | An id scheme so tests and commits can reference this spec |
| Constraints | Known limits: performance, platform, backward compatibility |
| Design notes | Enough of the "how" to catch architectural disagreement before coding starts |

Acceptance criteria read best in EARS form: "When `<trigger>`, the system
shall `<behavior>`." This keeps them testable instead of aspirational.

## Rigor levels

Not every change needs a full spec document. Scale the rigor to the size
and risk of the change:

1. **Spec-first** (default minimum): a short written spec exists before
   implementation, even if it is a few bullet points in the issue.
2. **Spec-anchored**: required for public API changes or architecture
   changes, backed by a standalone `docs/` document reviewed before
   implementation starts.
3. **Spec-as-source**: for large or heavily agent-assisted features, the
   spec is detailed enough that an agent implementing it needs no
   additional clarification, and the spec itself becomes the PR's review
   artifact alongside the diff.

## Traceability ids

Use a per-project prefix plus a domain code and a running number, e.g.
`FR-<DOMAIN>-<NN>` (functional requirement) or `AC-<AREA>-<NN>` (acceptance
criterion). Either scheme is fine: pick one per project and use it
consistently. Ids are append-only: never renumber or reuse one, even after
the requirement it named is removed.

Every acceptance criterion should be referenced by at least one test.
Every test that guards a specific requirement should reference that
requirement's id in a comment or the test name, so the mapping is
greppable in both directions.

## Where specs live

`docs/specification.md` for the project-level functional spec,
`docs/architecture.md` for structural decisions, and
`docs/<feature>.md` (or `docs/features/<feature>.md`) per feature. A
`docs/decisions.md` (ADR-style log) records why a constraint or gate was
loosened or changed, so "why is this weaker than the spec says" is always
answerable from git history.

## Gating rule

Do not start implementation work at a given V-cycle level (see
[integration.md](integration.md)) until the spec artifacts for that level
exist. The point is keeping an agent, or a human under deadline pressure,
from inventing requirements
mid-implementation.
