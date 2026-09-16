# Criticality

How much rigor a piece of code earns, and the procedure for breaking a
rule on purpose. This file scales the practices in
[style/defensive.md](../style/defensive.md) and
[tdd.md](tdd.md) to the cost of the failure, so that a plotting helper and
a control loop are not held to the same standard.

None of the projects in this family are pursuing certification. The rules
below are borrowed from standards that exist for certification, and the
borrowing is deliberate and partial. [What to skip](#what-to-skip) names
what was left behind and why, so that nobody reintroduces it out of a
sense that more process is safer.

## Levels

Assign a level per module, not per project, and record it in the module's
own documentation.

| Level | Meaning | Examples |
|---|---|---|
| **C0** | Failure costs a rerun | Plotting, report generation, developer scripts, visualization |
| **C1** | Failure produces a wrong answer a human will act on | Offline planning, map building, analysis pipelines, data conversion |
| **C2** | Failure produces a wrong command to a machine, or a wrong answer nobody reviews | Controllers, trajectory generation, collision checking, anything on a control path |

A module's level is the highest level of anything that depends on it. A
geometry helper called from a controller is C2, whatever else calls it.

## What each level requires

Cumulative: C2 requires everything C1 requires.

| Requirement | C0 | C1 | C2 |
|---|---|---|---|
| Zero compiler and linter warnings, enforced in CI | Yes | Yes | Yes |
| Tests exist for new behavior ([tdd.md](tdd.md)) | Yes | Yes | Yes |
| Public API documented, with the error and panic conditions stated | Recommended | Yes | Yes |
| Coverage gate | None | Project gate | Project gate, and no untested error path |
| Inputs validated at the public boundary, typed error returned | Recommended | Yes | Yes |
| Illegal states unrepresentable by construction | Recommended | Recommended | Yes |
| Every loop has a stated upper bound and a budget-exhausted result | No | Recommended | Yes |
| No recursion | No | Recommended | Yes |
| No allocation after construction on the step path | No | No | Yes |
| No panicking construct in library code, enforced by lint | Recommended | Yes | Yes |
| Assertion in every function longer than about ten lines | No | Recommended | Yes |
| Property-based tests over the documented invariants | No | Recommended | Yes |
| Mutation testing on the diff | No | Recommended | Yes |
| Deviation record required to break a rule above | No | Yes | Yes |

Requirements-based testing is the one activity that is never optional at
any level. ISO 26262-6 makes it highly recommended at every ASIL including
the lowest, which is a useful calibration: of everything in that standard,
the part that never relaxes is testing against stated requirements. The id
scheme in [sdd.md](sdd.md#traceability-ids) is how a project satisfies it.

## Deviations

Adapted from MISRA Compliance:2020, which is the one document in that
family published free and the only part worth adopting wholesale.

Every rule in this library carries one of three categories:

| Category | Meaning |
|---|---|
| **Mandatory** | Never violated. No deviation is possible |
| **Required** | Violated only with an approved deviation record |
| **Advisory** | Followed as far as is reasonable. A violation needs no record |

One asymmetry is the whole point of the scheme, and it is not negotiable:
**a category may be tightened, never loosened.** An advisory rule may be
made required or mandatory for a project. A required rule may be made
mandatory. A required rule may never be demoted to advisory, and a
mandatory rule may never be recategorized at all.

A deviation record states the rule, the circumstance, the justification,
the risk, and any compensating measure taken. It is approved by somebody
who did not write the code. The JPL standard is blunt about why: approval
by the engineer or the project lead who wants the waiver is not
sufficient. Deviation records live in `docs/decisions.md` alongside the
other decisions a reader would otherwise have to reconstruct from git
history.

Where a rule has a legitimate standing exception, mark it in the code with
a machine-readable comment rather than leaving it unwritten. JPL requires
exactly this for the one non-terminating loop a task is allowed:

```c
/* @non-terminating@ */
```

An exemption a tool can find is an exemption a reviewer can count.

## Verification tiers

Run cost determines where a check lives, not how much it is trusted.

| Tier | When | Contents |
|---|---|---|
| Every commit | Under a minute | Formatter, linter at deny-warnings, type checker, unit tests, coverage gate |
| Every pull request | Under fifteen minutes | Integration tests, property-based tests, mutation testing scoped to the diff, public API compatibility check |
| Scheduled | Overnight or weekly | Model checking, fuzzing with a time box, undefined-behavior interpreters, long-horizon simulation |

The property that matters more than the tiering is that one script runs
all of a tier, locally and in CI, with no difference between the two. See
[integration.md](integration.md#one-script-same-in-ci-and-local).

## Tools

No project here qualifies a compiler or an analyzer, and none should. The
useful residue of tool qualification is the set of operating constraints
that a qualified toolchain requires of its user, all of which are free:

- **Pin the toolchain** to an exact version, in a file, in the repository.
- **Commit the lockfile.** Resolve dependencies reproducibly.
- **Control the build environment.** Environment variables that change
  code generation are part of the build definition.
- **Build the final artifact from a clean tree.**
- **Treat every warning as an error** for that build.
- **Inventory every escape hatch.** Every unsafe block, every type-checker
  suppression, every lint exemption should be countable by a command.
- **Track known problems** in the toolchain and the dependencies, and
  audit dependencies on a schedule.

These come from the Ferrocene safety manual's constraints on users of its
qualified compiler, which is the clearest published list of what a
toolchain user owes the toolchain. Adopting them costs nothing and
survives a change of tools.

## What to skip

Deliberately not adopted, with the reason, so this decision does not get
relitigated:

- **Modified condition/decision coverage.** Required only at the highest
  assurance levels of DO-178C and ISO 26262. Support was removed from the
  Rust compiler in 2025 and no Python implementation exists. Reintroducing
  it is an accepted Rust project goal; track it, do not build on it.
- **Buying standards documents.** MISRA C and C++ are paid, C-family
  specific, and the free MISRA Compliance:2020 is the part that generalizes.
- **Formal methods as a blanket requirement.** IEC 61508 requires them
  only at its highest integrity level. Model checking is worth it where it
  is cheap, which is integer and index logic, and is a poor fit for
  floating-point numerical code, where bounded model checkers
  over-approximate the transcendental functions a navigation library lives
  on.
- **Full compliance artifacts.** An enforcement plan, a recategorization
  plan, and a per-delivery compliance summary exist to convince a third
  party. With no third party, they are paperwork. Keep the deviation
  records, which are useful internally; skip the rest.
- **Tool qualification itself.** See [Tools](#tools).
- **Rules whose rationale was a C-specific analysis limitation.** No
  function pointers, one level of pointer dereference, and the
  preprocessor restrictions do not transfer to a memory-safe language with
  closures and traits. Importing them spends review time forever and buys
  nothing.

## Sources

- MISRA Compliance:2020. https://misra.org.uk/app/uploads/2021/06/MISRA-Compliance-2020.pdf
- JPL Institutional Coding Standard for the C Programming Language, JPL DOCID D-60411, version 1.0, 2009.
- ISO 26262-6:2018, software unit and integration verification tables. https://www.iso.org/standard/68388.html
- IEC 61508-3:2010. https://webstore.iec.ch/publication/5517
- Hayhurst, Veerhusen, Chilenski and Rierson, "A Practical Tutorial on Modified Condition/Decision Coverage", NASA/TM-2001-210876. https://ntrs.nasa.gov/api/citations/20010057789/downloads/20010057789.pdf
- Ferrocene safety manual, user constraints. https://public-docs.ferrocene.dev/main/safety-manual/rustc/constraints.html
