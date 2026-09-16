# Defensive programming

Cross-language rules for code whose failure costs more than a rerun:
control loops, planners, anything that moves a machine or that another
project depends on. Language-specific enforcement lives in `languages/`;
this file states the rule and names the mechanism that checks it.

The title is a compromise. Meyer's Design by Contract and the older sense
of "defensive programming" are opposed positions, and
[Contracts](#contracts) says which one applies where. Read that section
before applying anything else here.

Rigor scales with criticality. See
[workflow/criticality.md](../workflow/criticality.md) for which of these
rules are mandatory at which level, and for the deviation procedure that
covers breaking one on purpose.

## Contracts

A precondition is the caller's obligation before a call, a postcondition
is the callee's obligation at return, and an invariant holds in every
observable state. Bertrand Meyer, "Applying Design by Contract", IEEE
Computer 25(10), 1992, states the non-redundancy rule as an absolute:

> Either you have the condition in the Require, or you have it in an If
> instruction in the body of the routine, but never in both.

The JPL Institutional Coding Standard takes the opposite position at a
module's public edge, requiring that "the validity of function parameters
shall be checked at the start of each public function."

Both are right, at different boundaries:

- **At a public API**, validate and return a typed error. A caller you
  cannot see cannot be given an obligation, and a library called from
  another language cannot assume the caller read anything.
- **Below that boundary**, apply Meyer's rule. Each internal invariant is
  checked once, by one party, as an assertion rather than a branch.

Oracle's Java assertion guide states the same split as a rule: do not use
assertions for argument checking in public methods, since the check is
part of the published contract and has to hold whether assertions are
enabled or not.

When a contract fails, Meyer allows exactly two responses in software:
retry after restoring the invariant, or give up on the contract and report
failure to the caller. Both restore the invariant first. In a language
with `Result`, the second is ordinary error propagation.

## Assertions

Holzmann's Power of Ten rule 5 sets a density target of two assertions per
function on average; JPL rule 16 weakens it to one assertion in any
function longer than ten lines. Prefer the JPL form: a per-function
average invites padding, and `assert(true)` counts toward it.

The published evidence is real but modest, and worth stating honestly
rather than overselling. Kudrjavets, Nagappan and Ball, "Assessing the
Relationship between Software Assertions and Code Quality", Microsoft
Research MSR-TR-2006-54, found Spearman correlations between assertion
density and fault density of -0.21 to -0.38 across two commercial
components. The paper's own threats-to-validity section concedes it cannot
rule out that assertion density proxies for developer care. Cite it as
support, never as proof of causation.

Rules that are not negotiable:

- **An assertion never has a side effect.** Holzmann states it; Oracle
  gives the reason, which is that assertions may be compiled out, so a
  program must not depend on the expression running.
- **An assertion is a Boolean test**, not a computation.
- **A failed assertion in a library returns an error.** Power of Ten rule
  5 requires "an explicit recovery action", and a library that aborts the
  host process has taken a decision belonging to the caller.
- **Never assert on an input that crossed a public boundary.** That is a
  contract check and belongs in the error path.

Two levels, and the split is about cost, not importance:

| Level | Use for | Enabled |
|---|---|---|
| Always-on assertion | A violation produces a wrong answer, not a slow one: non-finite input, empty required collection, index not already proven by the type | Every build |
| Debug-only assertion | An algebraic invariant that costs real time: a rotation matrix still orthonormal, a cost still decreasing, a tree still balanced | Development and test |

Hoare made the case against disabling checks in production in 1973, and
nobody has improved on it:

> it is absurd to make elaborate security checks on debugging runs, when
> no trust is put in the results, and then remove them in production runs,
> when an erroneous result could be expensive or disastrous.

Language notes. Rust splits these as `assert!` and `debug_assert!`, and
the standard library warns that downgrading one to the other is
"only encouraged after thorough profiling, and more importantly, only in
safe code". Python compiles `assert` to nothing under `-O`, so anything
that must hold in production is an explicit `raise`, never an `assert`.

## Failure response

The choice is not library versus embedded in the abstract. It is whether a
caller exists who can do something about the failure.

| Context | Response | Mechanism |
|---|---|---|
| Public API | Typed error, never a process abort | A result type and an extensible error enum |
| Internal code | Fail fast and loudly | Assertions |
| Control loop | Never abort. Hold the last valid command, degrade, reach a safe state | A supervisor around the algorithm, plus an explicit degraded mode |
| Foreign-language boundary | Convert, never unwind | See [languages/rs.md](../languages/rs.md#crates-called-from-another-language) |

NASA-GB-8719.13, the NASA Software Safety Guidebook, gives the freely
citable definitions, since the ISO and IEC equivalents are paywalled:

> **Fail-safe.** Ability to sustain a failure and retain the capability to
> safely terminate or control the operation.

> **Graceful degradation.** A planned stepwise reduction of function or
> performance as a result of failure, while maintaining essential function
> and performance.

The same document adds the property that matters most for a planner: no
safe state should ever transition to an unsafe state as a result of a
computer control action.

James Shore, "Fail Fast", IEEE Software 21(5), 2004, resolves the tension
between failing fast and staying up: keep the checks enabled in the field,
and handle the failure at a level that can degrade, because "a crash is
never appropriate" in delivered software and an error that reached a
customer is the hardest kind to reproduce.

## Make illegal states unrepresentable

Alexis King, "Parse, do not validate" (2019), draws the distinction that
matters: a validator checks a condition and throws away what it learned,
while a parser returns a value whose type carries the proof. The failure
mode has a name from language-theoretic security, **shotgun parsing**:
validation scattered through a program so that invalid input may already
have been acted on before anything rejects it.

Applied:

- **Parse once, at the boundary.** A constructor that can fail returns a
  type that later code cannot question.
- **Use a sum type where the cases are mutually exclusive.** Yaron Minsky
  gives the canonical example: a record with `last_ping_time`,
  `session_id` and `when_disconnected` all optional becomes an enum whose
  variants each carry only the fields that exist in that state.
- **Wrap physical quantities in distinct types.** `Radians` and `Degrees`
  cannot be swapped by accident. This is where the who_what rule in
  [naming.md](naming.md#the-who_what-rule-for-physical-and-measured-variables)
  meets the type system: the identifier names the quantity, and the type
  carries the unit.
- **Encode phase in the type where a protocol exists.** A handle that
  cannot be queried before it has converged, or a planner that cannot run
  before it is configured. Strom and Yemini named this **typestate** in
  1986; Rust gets it from ownership, by taking `self` by value and
  returning a different type.
- **Prefer total functions.** Widen the return type or narrow the argument
  type until no input is rejected.

## Bounded resources

Four rules, from Holzmann's Power of Ten (rules 1, 2 and 3) and the JPL
standard (rules 3, 4 and 5). They exist to make timing and stack usage
analyzable, and they pay off even where nobody performs the analysis,
because each one removes a class of unbounded behavior.

1. **Every loop has a statically determinable upper bound.** Where the
   iteration count is data-dependent, add an explicit cap, and return an
   error when it is hit rather than continuing. Holzmann gives exactly
   this pattern for list traversal.
2. **No recursion, direct or indirect.** An acyclic call graph is what
   makes a static stack bound derivable. Replace tree and graph recursion
   with an explicit stack of bounded capacity, which also gives the memory
   bound for free.
3. **No dynamic allocation after initialization.** Allocators have
   unpredictable latency and introduce an out-of-memory failure path. The
   realistic scoped version for a library: allocate at construction, and
   allocate nothing inside a step or solve call.
4. **No blocking input or output on the control path.** No file, socket,
   or console access reachable from a step call. Diagnostics are returned
   values.

Rule 1 has a documented exception for a deliberately non-terminating
server loop. JPL requires such a loop to carry a machine-readable marker
comment rather than an unwritten exemption, which is a pattern worth
copying for any exception to any rule here.

Every algorithm entry point that searches, samples, or iterates takes an
explicit budget (`max_iterations`, `max_samples`, `max_expansions`) and
returns a result that distinguishes convergence from budget exhaustion. Do
not offer an unlimited variant of the same function that a real-time
caller uses.

## Numerical rules

These apply to any code doing geometry, estimation, or control. The
reference throughout is David Goldberg, "What Every Computer Scientist
Should Know About Floating-Point Arithmetic", ACM Computing Surveys 23(1),
1991.

### Comparison

Never compare two independently computed floating-point values for
equality. The rule is narrower than "never use `==` on floats": comparing
against a sentinel you stored yourself, or testing a value you are about
to divide by, is correct and clippy's `float_cmp` exempts both.

Approximate equality is **not transitive**. Goldberg states it directly:
`a ~ b` and `b ~ c` does not imply `a ~ c`. So a tolerance test is never a
sort key, a hash key, or a deduplication criterion.

Use an absolute tolerance near zero and a relative or ULP tolerance away
from it, per Bruce Dawson, "Comparing Floating Point Numbers, 2012
Edition", whose own conclusion is that "there is no silver bullet."

**Tolerances are domain constants in physical units**, named and defined
once: a position tolerance in meters, an angle tolerance in radians, a
time tolerance in seconds. A single project-wide epsilon is wrong, because
a fixed relative error expressed in ulps wobbles by a factor of the radix
across the exponent range. Do not use the language's machine epsilon as a
domain tolerance: clippy's own documentation warns against it, and
Goldberg's epsilon differs from Rust's `f64::EPSILON` by a factor of two
because the two terms mean different things.

### Ordering

A NaN compares unordered with everything, including itself, so
floating-point values are only partially ordered. IEEE 754-2019 clause
5.11 states the consequence that breaks naive code:

> When NaNs are present, operands have the unordered relation, so
> trichotomy does not apply. For example, NOT (X < Y) is not logically
> equivalent to (X >= Y).

Sorting, priority queues, and map keys need a total order. Use the
language's `totalOrder` binding (Rust's `f64::total_cmp`), or parse the
value into a not-a-number-free type at construction and keep it that way.
Never reach for a partial comparison and unwrap the failure case.

### Propagation and domain errors

A NaN propagates through every subsequent operation and every comparison
against it returns false, so an optimizer silently selects a garbage
candidate rather than failing. Check finiteness at stage boundaries, not
inside the inner loop, and always on values crossing a public API.

Guard a near-zero denominator at the point where the degenerate input is
constructed, not at the division site. A zero-length segment, a singular
Jacobian, and a zero-norm direction vector are domain errors in the
geometry and deserve named errors, not a magic epsilon patched in where
the division happens. Where a conditioning measure exists, LAPACK's
convention is worth copying: compute the reciprocal condition number to
avoid overflow, clamp it to machine epsilon before dividing, and treat a
relative error bound at or above 1 as total loss of accuracy.

Two rules from Nicholas Higham, "Seven Sins of Numerical Linear Algebra":
never invert a matrix when you can solve the system instead, and never use
a determinant to test near-singularity, since scaling a matrix scales its
determinant without changing its conditioning at all. Conditioning comes
from singular values, never from eigenvalue ratios.

### Angles

Never subtract two angles directly; the result is wrong by a full turn
across the branch cut. Normalize a difference with `atan2(sin(d), cos(d))`
or an explicit wrap. The durable fix is a type whose subtraction wraps, so
the failure mode stops existing rather than being documented.

ROS REP 103 standardizes the radian as the angular unit and is worth
following for any project exchanging data with a robotics stack. It says
nothing about wrapping; that rule is engineering practice, not a standard,
and this file is its source.

### Control output

Every controller that integrates needs an anti-windup path, and every
command leaving a control module passes through one saturation function
and one rate limiter. Karl Johan Astrom states the reason a saturated loop
is a safety concern rather than a tuning annoyance:

> When the output saturates, the feedback loop is broken and the system
> runs as an open loop because the actuator will remain at its limit
> independently of the process output.

Back-calculation is the default remedy: feed the difference between the
commanded and the applied output back into the integrator through a gain.
The signal is exactly zero when unsaturated, so it costs nothing in normal
operation. Astrom brackets the tracking time constant between the
derivative and integral time constants.

Conditional integration, which simply stops integrating under some
condition, is an unanalyzed nonlinearity. Offer it only as a named
non-default alternative, and carry the warning with it.

A rate limit is a separate defense from a magnitude clamp, and its failure
mode is different: it injects phase lag rather than exhausting authority.
NASA TN D-7900, analyzing pilot-induced oscillation on the YF-12, found
that "SAS rate limits are more detrimental than position limits." Report
the two saturations separately.

Report, never decide. A library counts saturated steps and reports the
integral of the requested-minus-applied difference; the integrating system
decides what a sustained saturation means. No primary source defines a
persistence threshold, so any number a project picks is a local decision
that belongs in `docs/decisions.md`.

## Sampling and time

A controller takes the elapsed interval as an argument to every step, and
never reads a clock itself. A library that calls the system clock inside
its step function has made a syscall decision for the caller and has made
itself untestable deterministically.

Where the interval appears in a precomputed coefficient, either recompute
when it changes beyond a stated tolerance, or document that the
construction-time period is authoritative and the caller must hold it.
State which; do not leave it ambiguous.

Split computing a command from committing it, so the caller can send the
command to the actuator between the two calls. Astrom and Murray note this
is the cheapest available latency reduction and that it is "seldom used in
commercial systems." The same split gives the anti-windup path and any
observer the applied value rather than the requested one, which is the
structural fix for feeding the wrong value back.

Jitter has its own stability margin, separate from the phase margin.
Cervin and colleagues, "The Jitter Margin and Its Application in the
Design of Real-Time Control Systems", RTCSA 2004, separate the constant
part of the input-output delay from the time-varying part and show the
latter needs its own analysis.

## What only a human can check

Every rule below prevents real defects and none of them has a linter. Put
them on a review checklist rather than pretending they are automated.

1. Whether a precondition sits on the correct side of the boundary. No
   tool knows which party should own a condition.
2. Whether a sum type genuinely makes illegal states unrepresentable, or
   is a record of optional fields wearing an enum costume.
3. Whether parsing happens at the boundary, or has spread into the
   interior as shotgun parsing.
4. Whether an assertion is meaningful. Density is countable; truth is not.
5. Whether every integrator has an anti-windup path, and every command
   passes exactly one saturation and one rate limiter.
6. Whether a tolerance is the right tolerance. A lint catches `==`;
   nothing catches a wrong epsilon.
7. Whether a loop bound is real, or a comment above a convergence test.
8. Whether the degraded mode is safe, and reachable from every state the
   system can enter.
9. Whether the failure response matches the context, per
   [Failure response](#failure-response).

## Sources

- Bertrand Meyer, "Applying Design by Contract", IEEE Computer 25(10), 1992. https://se.inf.ethz.ch/~meyer/publications/computer/contract.pdf
- Gerard J. Holzmann, "The Power of Ten: Rules for Developing Safety-Critical Code", IEEE Computer, June 2006. https://dl.acm.org/doi/10.1109/MC.2006.212
- JPL Institutional Coding Standard for the C Programming Language, JPL DOCID D-60411, version 1.0, 2009.
- Kudrjavets, Nagappan and Ball, "Assessing the Relationship between Software Assertions and Code Quality", MSR-TR-2006-54. https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/tr-2006-54.pdf
- C. A. R. Hoare, "Hints on Programming Language Design", Stanford AIM-224, 1973. http://i.stanford.edu/pub/cstr/reports/cs/tr/73/403/CS-TR-73-403.pdf
- James Shore, "Fail Fast", IEEE Software 21(5), 2004. https://martinfowler.com/ieeeSoftware/failFast.pdf
- NASA Software Safety Guidebook, NASA-GB-8719.13, 2004. https://standards.nasa.gov/sites/default/files/standards/NASA/Baseline/0/nasa-gb-871913.pdf
- Alexis King, "Parse, do not validate", 2019. https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/
- Yaron Minsky, "Effective ML", Jane Street, 2011. https://blog.janestreet.com/effective-ml-revisited/
- Strom and Yemini, "Typestate: A Programming Language Concept for Enhancing Software Reliability", IEEE TSE 12(1), 1986.
- David Goldberg, "What Every Computer Scientist Should Know About Floating-Point Arithmetic", ACM Computing Surveys 23(1), 1991. https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html
- IEEE 754-2019. https://ieeexplore.ieee.org/document/8766229
- Bruce Dawson, "Comparing Floating Point Numbers, 2012 Edition". https://randomascii.wordpress.com/2012/02/25/comparing-floating-point-numbers-2012-edition/
- Nicholas J. Higham, "Seven Sins of Numerical Linear Algebra", 2022. https://nhigham.com/2022/10/11/seven-sins-of-numerical-linear-algebra/
- LAPACK Users' Guide, 3rd edition, section 4.4. https://www.netlib.org/lapack/lug/node80.html
- Karl Johan Astrom, "Control System Design", chapter 6, 2002. https://www.cds.caltech.edu/~murray/courses/cds101/fa02/caltech/astrom-ch6.pdf
- Astrom and Murray, "Feedback Systems", 2nd edition, sections 11.4 and 11.5. https://www.cds.caltech.edu/~murray/books/AM08/pdf/fbs-public_24Jul2020.pdf
- Smith and Berry, "Analysis of Longitudinal Pilot-Induced Oscillation Tendencies of YF-12 Aircraft", NASA TN D-7900, 1975. https://ntrs.nasa.gov/api/citations/19750008488/downloads/19750008488.pdf
- Cervin, Lincoln, Eker, Arzen and Buttazzo, "The Jitter Margin and Its Application in the Design of Real-Time Control Systems", RTCSA 2004. https://www.cis.upenn.edu/~lee/04cis700/papers/CLE+04.pdf
- ROS REP 103, Standard Units of Measure and Coordinate Conventions. https://github.com/ros-infrastructure/rep/blob/master/rep-0103.rst
