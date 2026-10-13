---
name: Feature request
about: Propose new functionality, following the SDD workflow
labels: enhancement
---

## Summary

One or two sentences: what this adds and why.

## Motivation

What problem does this solve? What happens if it is not built?

## Spec

While this change is open, this issue is the working contract: the spec
and the plan together. Link `docs/<feature>.md` once that file exists. A
plan that exists only in a chat does not count. Which changes need the
full cycle, and which rigor level applies, is in `workflow/sdd.md`.

## Acceptance criteria

State behavior first, before endpoint or format details. Numbered,
testable, EARS-style where possible ("When `<trigger>`, the system shall
`<behavior>`"). Assign a traceability id per criterion,
`FR-<DOMAIN>-<NN>` or `AC-<AREA>-<NN>` (see `workflow/sdd.md`):

- [ ] `<ID>`:
- [ ] `<ID>`:

## Edge cases

Boundaries, written as behavior, before any endpoint or payload format:

- [ ] Empty input, missing input, and input at the declared limits:
- [ ] The failure a caller can cause, and what that caller observes:
- [ ] Repeated, overlapping, or out-of-order requests, where the feature
      can receive them:

## Proposed approach

Enough of the design to catch disagreement before implementation starts.
It does not need to be the final design.

## Out of scope

What this explicitly does not cover, so reviewers do not expect it.

## References

Related issues, prior art, design docs.
