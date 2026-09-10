# How SDD, the V-cycle, and TDD combine

[sdd.md](sdd.md) and [tdd.md](tdd.md) describe two practices in isolation;
this is how they fit into one loop per task, sometimes called the V-cycle.

## The V-cycle

Each unit of work (a feature, a fix, a refactor) is one complete V, not a
one-way waterfall:

```
1. Document   →  Issue/PR links to a spec (docs/); acceptance criteria defined
2. Architecture →  Interfaces and test plan defined before implementation
3. Implement  →  TDD red/green/refactor fills in the stubs
4. Verify     →  Local validation script; coverage gate; all tests green
5. Review     →  PR against the spec; CI must pass; visual/behavioral
                  evidence attached where relevant
6. Merge      →  Human-only: see review.md
```

The "V" shape is the point: level 1 (spec) pairs with level 5 (review
against that spec), level 2 (architecture/test plan) pairs with level 4
(verification against that plan). Writing the spec and the verification
plan together, before implementation, is what keeps them honest: writing
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

## When a toolkit drives the cycle

cc-sdd runs the same V under different names, and a project using it should
recognize the mapping rather than run two processes side by side:

| V-cycle step | cc-sdd phase |
|---|---|
| 1, document | `kiro-discovery`, then `kiro-spec-requirements` |
| 2, architecture | `kiro-spec-design`, then `kiro-spec-tasks` |
| 3, implement | `kiro-impl`, one task per iteration |
| 4, verify | The project's validation script, unchanged |
| 5, review | The reviewer pass, plus a human reading the diff |
| 6, merge | Human only, unchanged |

Steps 4 and 6 are the ones a toolkit does not get to redefine. A phase
gate inside a skill is an agent agreeing with itself; the validation script
and the human merge are what make it real.

## Why this order, specifically

Writing the test/validation plan (step 2) before the implementation (step
3) is what makes this TDD rather than just "we have tests." An
implementation-first approach tends to produce tests that confirm what the
code happens to do, not what it is supposed to do: which defeats the
purpose of having tests at all.

## One script, same in CI and local

The verification step (4) should be a single script (`./scripts/validate.sh`
or equivalent) that runs identically whether invoked locally or in CI,
never a set of instructions that differ between the two. This is what
makes "it passed locally" and "CI is green" mean the same thing, and it is
what an agent should run in a loop until clean before ever opening a PR.
