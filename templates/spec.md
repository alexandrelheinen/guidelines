# Spec for `<feature name>`

Status: draft | reviewed | implemented

See [workflow/sdd.md](../workflow/sdd.md) for how this fits into the
overall process, and [workflow/integration.md](../workflow/integration.md)
for how it feeds the V-cycle.

This template is for a project writing its specs by hand under `docs/`. A
project running cc-sdd generates `requirements.md`, `design.md`, and
`tasks.md` from its own templates instead, and the sections below become a
checklist for what those files still have to contain.

While a change is open, the issue is the working contract. This document
is where that contract lives once it has to outlast the issue, and the
project's README links to it. State behavior first, before endpoint or
format details. A criterion that names a route, a field, or a payload
before it names the behavior freezes the design into the requirement.

## Intent

What problem this solves and for whom. One paragraph.

## Scope

**In scope:**
-

**Out of scope:**
-

## Acceptance criteria

Numbered and testable, EARS-style where possible. Behavior first, before
any endpoint or format. Assign a traceability id per criterion: pick one
scheme per project (`FR-<DOMAIN>-<NN>` or `AC-<AREA>-<NN>`) and stay
consistent:

- `<ID>`: When `<trigger>`, the system shall `<behavior>`.
- `<ID>`: When `<trigger>`, the system shall `<behavior>`.

## Edge cases

Boundaries the acceptance criteria cover, each one written as behavior:

- Empty input, missing input, and input at the declared limits.
- The failure a caller can cause, and what that caller observes.
- Repeated, overlapping, or out-of-order requests, where the feature can
  receive them.

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
