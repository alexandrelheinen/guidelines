# How SDD, the V-cycle, and TDD combine

[sdd.md](sdd.md) and [tdd.md](tdd.md) describe two practices in isolation;
this is how they fit into one loop per task, sometimes called the V-cycle.

## The V-cycle

A change that enters the cycle is one complete V, not a one-way
waterfall:

```
1. Document    →  The issue holds the working contract: spec and plan
2. Architecture →  Interfaces and test plan, reviewed before implementation
3. Implement   →  TDD red/green/refactor fills in the stubs
4. Verify      →  Local validation script; coverage gate; all tests green
5. Review      →  PR against the spec; CI must pass; visual/behavioral
                   evidence attached where relevant
6. Merge       →  Human-only: see review.md
```

Steps 1, 2, and 5 are human review gates. See
[Three review gates](#three-review-gates).

The "V" shape is the point: level 1 (spec) pairs with level 5 (review
against that spec), level 2 (architecture/test plan) pairs with level 4
(verification against that plan). Writing the spec and the verification
plan together, before implementation, is what keeps them honest: writing
the verification plan after the code exists tends to describe what the
code already does.

## When the cycle applies

The six steps apply to a change of architecture, a change of usage, a
new feature, or a new module. For those, steps 1 and 2 are real
documents, and a human reviews each before step 3 starts. The
relationship between that spec and the code is a rigor level in
[sdd.md](sdd.md#rigor-levels). Spec-anchored is the practical default:
the permanent documents evolve with the code, and tests in CI keep them
aligned.

A small, obvious fix skips the six steps when its cause is already
known. When the cause is uncertain, a short contract in the issue is
enough. The regression test in [tdd.md](tdd.md) and the human merge in
[review.md](review.md) still apply. The skip covers the spec and the
plan.

Stop refining the spec once further refinement would cost more than
fixing the misunderstanding in the code. A change across several files,
or a design that would be hard to reverse, still gets a contract in the
issue. The same rule is stated from the spec side in
[sdd.md](sdd.md#applicability).

## Three review gates

A human reviews three times, at steps 1, 2, and 5. The gates catch
different errors. One review at the pull request lets a wrong problem
or a wrong plan reach code. An automated pass does not close a gate.
When an agent drafts the artifact, a human reviews it. The same rule is
recorded from the spec side in [sdd.md](sdd.md#three-review-gates).

| Gate | Step | The error it catches |
|---|---|---|
| Spec | 1, before the plan is treated as settled | The spec addresses the wrong problem, leaves a boundary unnamed, or states a criterion nobody can test |
| Plan | 2, before implementation | The approach cannot meet the accepted criteria, or it commits to a decision that would be expensive to undo |
| Code review | 5, before merge | The diff does something other than the accepted spec and plan, including behavior no test expresses. [review.md](review.md) is how this gate is run |

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
and the human merge are what make it real. The spec, plan, and
code-review gates above stay human decisions, whichever phase name the
toolkit uses.

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
