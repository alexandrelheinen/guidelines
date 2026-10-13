# Specification-Driven Development (SDD)

Write the acceptance criteria before generating code, for every change
that enters the cycle. How long those criteria stay aligned with the
code depends on the rigor level below. While a change is open, the
issue is the working contract, and a pull request links to that issue.

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

These name the relationship between a spec and the code, for a change
that is inside the cycle. Whether a change enters the cycle is
[Applicability](#applicability). How much verification a module earns is
a separate scale, in [criticality.md](criticality.md).

1. **Spec-first.** The acceptance criteria are written before any code
   is generated. Afterward the spec may age: nothing in the workflow
   requires the document to move when the code moves.
2. **Spec-anchored** (the practical default). The permanent documents
   evolve with the code. Tests running in CI keep the two aligned. Every
   criterion is referenced by a test, per
   [Traceability ids](#traceability-ids), and a drift between the
   document and that test fails the build.
3. **Spec-as-source.** A change to the spec is what drives the change to
   the code. That direction needs strong validation before it is trusted.
   A human has reviewed the spec, the criteria are testable, and the
   tests fail when the spec and the code diverge.

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

A project running the cc-sdd toolkit keeps its specs where cc-sdd reads
them, `.kiro/specs/<feature>/`, holding `requirements.md`, `design.md`, and
`tasks.md` per feature. The toolkit owns that lifecycle, so fighting it
buys nothing.

Every other project uses `docs/`: `docs/specification.md` for the
project-level functional spec, `docs/architecture.md` for structural
decisions, and `docs/<feature>.md` per feature.

Either way `docs/decisions.md` holds the ADR-style log recording why a
constraint or gate was loosened or changed, so "why is this weaker than the
spec says" is always answerable. That file stays in `docs/` in both layouts,
because it outlives any one spec.

The required elements above do not change with the location. A cc-sdd
`requirements.md` still has to carry intent, scope, testable acceptance
criteria, traceability ids, and constraints, whatever the template
generated.

## The issue is the working contract

While a change is open, the issue holds the working contract: the spec
and the plan together. A plan counts only when the issue records it. A
chat, including a session with an agent, ends when the session ends, so
a plan that exists only there is not part of the contract.

At merge, whatever has to outlive the issue moves into
`docs/<feature>.md`, and the project's README links to that file, so a
reader finds the feature there.

A project running cc-sdd already keeps a durable spec in
`.kiro/specs/<feature>/`, which is where that toolkit reads it. Copying
the same text into `docs/<feature>.md` would split the source of truth,
so those files remain the durable home and the README indexes them. Add
`docs/<feature>.md` only for what the toolkit files do not already hold.
`docs/decisions.md` stays the log for a loosened gate in both layouts,
as [Where specs live](#where-specs-live) already requires.

## Applicability

The full cycle in [integration.md](integration.md) applies to a change
of architecture, a change of usage, a new feature, or a new module.

A small, obvious fix skips the cycle when its cause is already known.
When the cause is uncertain, a short contract in the issue is enough:
the intent, the suspected cause, and the behavior that will count as
fixed. The regression test in [tdd.md](tdd.md) and the human merge in
[review.md](review.md) still apply. The skip covers the spec and the
plan.

Stop refining a spec once further refinement would cost more than
repairing the misunderstanding in the code. A change across several
files, or a design that would be hard to reverse, still gets a contract
in the issue. The cost limit caps the polishing, and the contract stays.

## Three review gates

Three human reviews sit on the cycle. They catch different errors, so
one review at the end lets the earlier error through. An automated pass
does not close a gate. When an agent drafts the artifact, a human
reviews it.

| Gate | When | The error it catches |
|---|---|---|
| Spec | After the spec is written, before the plan is treated as settled | The spec addresses the wrong problem, leaves a boundary unnamed, or states a criterion nobody can test |
| Plan | After the plan is written, before implementation starts | The approach cannot meet the accepted criteria, or it commits to a decision that would be expensive to undo |
| Code review | On the pull request, before merge | The diff does something other than the accepted spec and plan, including behavior no test expresses |

How the gates sit on the six steps is in
[integration.md](integration.md#three-review-gates). How the code-review
gate is run is in [review.md](review.md).

## Gating rule

For a change inside the cycle, do not start implementation at a given
V-cycle level (see [integration.md](integration.md)) until the spec and
the plan for that level exist and a human has reviewed both. The point
is keeping an agent, or a human under deadline pressure, from inventing
requirements mid-implementation.

This survives contact with a toolkit that offers to skip the spec.
cc-sdd's discovery step can route a request straight to implementation,
and that routing is a suggestion, not permission.
[Applicability](#applicability) is what decides a skip, and a human is
the one who applies it. A skill may not waive a gate on the human's
behalf. See
[integrations/toolkits.md](../integrations/toolkits.md#arbitration).
