# Spec for `<feature name>`

Status: draft | reviewed | implemented

See [workflow/sdd.md](../workflow/sdd.md) for how this fits into the
overall process, and [workflow/integration.md](../workflow/integration.md)
for how it feeds the V-cycle.

This template is for a project writing its specs by hand under `docs/`. A
project running cc-sdd generates `requirements.md`, `design.md`, and
`tasks.md` from its own templates instead, and the sections below become a
checklist for what those files still have to contain.

## Intent

What problem this solves and for whom. One paragraph.

## Scope

**In scope:**
-

**Out of scope:**
-

## Acceptance criteria

Numbered and testable, EARS-style where possible. Assign a traceability id
per criterion: pick one scheme per project (`FR-<DOMAIN>-<NN>` or
`AC-<AREA>-<NN>`) and stay consistent:

- `<ID>`: When `<trigger>`, the system shall `<behavior>`.
- `<ID>`: When `<trigger>`, the system shall `<behavior>`.

## Traceability

How each acceptance criterion above maps to tests, once they exist:

| ID | Test(s) |
|---|---|
| `<ID>` | |

## Constraints

Known limits: performance budgets, platform support, backward
compatibility requirements, security/privacy requirements.

## Design notes

Enough of the "how" for a reviewer to catch an architectural disagreement
before implementation starts. Not a full implementation plan: just the
decisions that would be expensive to reverse later.

## Open questions

Anything still unresolved. Do not let these block starting work on the
parts that are already clear: but do not implement the unresolved parts
either.
