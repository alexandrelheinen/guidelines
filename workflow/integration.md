# SDD + V-cycle + TDD — how they combine

[sdd.md](sdd.md) and [tdd.md](tdd.md) describe two practices in isolation;
this is how they fit into one loop per task, sometimes called the V-cycle.

## The V-cycle

Each unit of work — a feature, a fix, a refactor — is one complete V, not a
one-way waterfall:

```
1. Document   →  Issue/PR links to a spec (docs/); acceptance criteria defined
2. Architecture →  Interfaces and test plan defined before implementation
3. Implement  →  TDD red/green/refactor fills in the stubs
4. Verify     →  Local validation script; coverage gate; all tests green
5. Review     →  PR against the spec; CI must pass; visual/behavioral
                  evidence attached where relevant
6. Merge      →  Human-only — see review.md
```

The "V" shape is the point: level 1 (spec) pairs with level 5 (review
against that spec), level 2 (architecture/test plan) pairs with level 4
(verification against that plan). Writing the spec and the verification
plan together, before implementation, is what keeps them honest — writing
the verification plan after the code exists tends to just describe what
the code already does.

## Applying it at different scales

- **Small fix**: still goes through all six steps, but steps 1-2 might be a
  few sentences in the issue rather than a standalone document. See
  [sdd.md § Rigor levels](sdd.md#rigor-levels).
- **New public API or architecture change**: steps 1-2 require a real
  `docs/` artifact reviewed before step 3 starts.
- **Large or agent-assisted feature**: the spec becomes detailed enough to
  serve as the review artifact in step 5 alongside the diff itself.

## Why this order, specifically

Writing the test/validation plan (step 2) before the implementation (step
3) is what makes this TDD rather than just "we have tests." An
implementation-first approach tends to produce tests that confirm what the
code happens to do, not what it's supposed to do — which defeats the
purpose of having tests at all.

## One script, same in CI and local

The verification step (4) should be a single script (`./scripts/validate.sh`
or equivalent) that runs identically whether invoked locally or in CI —
never a set of instructions that differ between the two. This is what
makes "it passed locally" and "CI is green" mean the same thing, and it's
what an agent should run in a loop until clean before ever opening a PR.
