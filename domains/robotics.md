# Robotics

Practice for software that moves a machine: mobile robots, manipulators,
and the planning and control libraries they are built from. Language
rules live in `languages/`, the cross-language defensive rules in
[style/defensive.md](../style/defensive.md), and the rigor levels in
[workflow/criticality.md](../workflow/criticality.md). This file covers
what is specific to the domain.

The organizing idea comes from ISO 21448 clause 4.4.3, and it settles most
arguments about scope: a reusable library is an **element out of
context**. It is developed against documented assumptions about its use,
and it ships those assumptions together with integration requirements the
integrating system must discharge. A library does not make a safety claim.
It states what it assumes, what it checks, what it returns when it
declines, and what somebody else has to do.

## Which standards apply

Getting this wrong is common and expensive, so start here.

| Standard | Applies to | Note |
|---|---|---|
| ISO 3691-4:2023 | Driverless industrial trucks, which its scope defines to include automated guided vehicles and autonomous mobile robots | The governing type-C standard for a mobile robot in Europe |
| ANSI/A3 R15.08 | Industrial mobile robots, in three parts | The US counterpart |
| ISO 10218-1:2025 and ISO 10218-2:2025 | Industrial robots and robot systems | Part 2 excludes mobile platforms, driverless industrial trucks, and tele-operated manipulators in its scope clause |
| ISO 13849-1:2023 | Safety-related parts of control systems, any machinery | Fourth edition added clause 7 on software and annex N on fault avoidance in software design |
| IEC 62061:2021 | The same ground, from the functional-safety side | Requires verification of safety-related software with stated independence from its author |
| ISO 21448:2022 | Hazardous behavior from functional insufficiency rather than from malfunction | The right model for a planner that is correct and still behaves badly |
| ISO 34502:2022 | Scenario-based safety evaluation | Annex F covers qualifying the simulator itself |
| IEC 60204-1 and ISO 13850 | Stop functions and emergency stop | See [Stopping](#stopping) |

Two corrections worth stating outright, because both are widely assumed
the other way. **ISO 10218-2 does not cover mobile robots**, so a
navigation stack looks to ISO 3691-4 instead. And **ISO/TS 15066 is no
longer where collaborative-operation requirements live**: the 2025 edition
of ISO 10218 absorbed them as normative content and retired the terms
"collaborative robot" and "collaborative operation", on the grounds that
collaboration is a property of an application rather than of a machine.

Performance level and safety integrity level attach to a safety function
realized in a subsystem, including its hardware architecture, diagnostic
coverage, and common-cause analysis. **No amount of test coverage on a
library produces a performance level.** What a library can do is be usable
inside such a subsystem.

## Functional insufficiency, not just malfunction

ISO 21448 separates a malfunction of the electronics from a **functional
insufficiency**: the algorithm does exactly what it was specified to do,
and the specification was wrong or incomplete for the situation. Its
introduction names the causes, including lack of robustness with respect
to sensor input variation and "the unexpected behaviour due to decision
making algorithm".

Two kinds, and the distinction decides the fix:

- A **specification insufficiency** means the requirement was wrong or
  silent. The fix is a requirement, not code.
- A **performance insufficiency** means the technical capability cannot
  meet a correct requirement. The fix is a functional restriction, a
  different algorithm, or handing authority elsewhere.

A **triggering condition** is the specific condition of a scenario that
turns an insufficiency into hazardous behavior. Finding them is the work.

ISO 21448 treats the planner as a first-class element with its own
insufficiency analysis, and the published planner case study found exactly
the failure modes a planning library has: a prediction horizon too short
to stop within, deadlock from over-conservative behavior when right of way
is ambiguous, optimizer infeasibility when a constraint is already
violated at the current step, and oscillation from controller and planner
interaction.

Its architectural remedy is worth copying directly. Distinguish solver
outcomes rather than collapsing them: converged to a local minimum,
feasible but suboptimal, certified infeasible, and no feasible point found
without a certificate. Accept a feasible suboptimal solution where a
terminal constraint preserves recursive feasibility, fall back to a backup
planner, and only when neither works, **request emergency operation from
the encompassing system** rather than inventing one.

## Stopping

Stop categories come from IEC 60204-1:

| Category | Behavior | The software's role |
|---|---|---|
| 0 | Immediate removal of power to the actuators, uncontrolled | None, by design. A library must not be on this path |
| 1 | Controlled stop with power available, then power removed once stopped | Owns the deceleration profile, which must terminate in bounded time because power removal is scheduled against it |
| 2 | Controlled stop, power left on | The only category a control library can implement end to end |

ISO 13850 governs emergency stop, and four of its requirements constrain
software directly. The function is available in all operating modes and
overrides everything else. It is a **complementary** protective measure
and never a substitute for safeguarding, so a library that markets a
"safe stop" as risk reduction is making a claim the standard forbids. The
command's effect is sustained until a manual reset, and that reset "shall
not restart the machinery but only permit restarting". And only category 0
or 1 is permitted: **category 2 cannot be used for an emergency stop.**

Three consequences for any library. No code path re-enables motion as a
side effect of a reset, which means "cleared" and "enabled" are separate
states. Stopping the stack leaves no half-applied command latched
anywhere, since the standard requires that using the stop not oblige the
operator to reason about the consequences. And a library method named
`stop` is a normal stop or at best a category 2 stop, so calling it an
emergency stop in an API is a naming error with safety consequences.

## Supervision

**The watcher is outside the watched.** A library supplies a liveness
signal and never supervises itself.

Two deployable patterns, both documented. A hardware watchdog through the
Linux device API resets the system when writes stop, and treats closing
the device without the magic sequence as a crash. A supervisor heartbeat
through the systemd protocol carries the one concrete number worth writing
down: **send the keep-alive every half of the configured interval.**

On a middleware that already has this, use it rather than reinventing it.
ROS 2 provides Deadline (maximum expected time between messages), Lifespan
(after which a message is stale and silently dropped), and Liveliness with
a lease, each with its own event.

An enabling or deadman input is **a level, not an edge**. Sample it every
cycle, give it its own staleness deadline, and treat a missing fresh
sample as not-enabled. A library accepts an enable input rather than
inferring permission from having been called. A three-position enabling
device removes the enable both when released and when squeezed, so
"not pressed" is never sufficient evidence of permission.

**Degraded mode is a specified mode with its own limits, not an error
path.** ISO 21448 lists functional restriction as a legitimate measure
alongside modifying the system and handing over authority, and the
published planner study restricted maximum speed so that a stop fits
inside the prediction horizon rather than making the planner cleverer. Put
the restricted envelope behind the same type as the nominal envelope, so
entering degraded mode is a different limit set through one API rather
than a different code path.

## Input validation

**Validity is a windowed state, not a per-sample boolean.** This is the
single most useful pattern in this file, and the AUTOSAR E2E protocol
specifies it completely and publicly. Its state machine carries `NoData`
(wait for the first reception, do not use data), `Init` (initializing, do
not use data), `Valid` (within limits, data is usable), and `Invalid` (not
within limits, do not use data). Transitions are driven by an error count
and an ok count over a sliding window against configured thresholds, so
one bad sample does not invalidate an input and recovery requires a run of
good ones.

The same specification maps each communication fault to the mechanism that
detects it: a **counter** detects repetition, loss, delay, incorrect
sequence, and a blocked channel; a **data identifier** detects insertion,
masquerading, and incorrect addressing; a **checksum** detects corruption.

On estimator inputs, the normalized innovation squared is chi-square
distributed with the measurement dimension as degrees of freedom, which
makes a per-update gate a standard consistency test with a principled
threshold rather than a tuned constant.

Four documented responses to a failed check, and a library should let the
integrator choose per input rather than hardcoding one:

1. **Reject the sample and keep running.** Valid when the estimator
   propagates on its own.
2. **Predict without correcting.** The state evolves under the model and
   the covariance grows, which is the honest signal.
3. **Refuse to produce output.** Return a typed failure and let the caller
   decide.
4. **Request fallback or emergency operation.** The library signals; the
   system acts.

**What a library must never do is return a plausible-looking control
computed from data it knows is invalid.** That is the rule the other four
exist to serve.

Specific rate-of-change thresholds per sensor, holding the last good value
for some number of cycles, and averaging two disagreeing sensors are all
folklore. No published source gives defensible numbers. The windowed state
above is the principled replacement, because it turns "how many bad
samples before I stop trusting this" into a configured, testable
parameter.

## Frames, units, and time

Adopt the ROS conventions even in a stack with no ROS in it, because they
are written down and the alternatives are not. REP 103 fixes SI units,
meters and radians, right-handed frames, and fixed-axis roll-pitch-yaw.

REP 105 carries the rule with teeth: the odometry frame "is guaranteed to
be continuous", evolving smoothly without discrete jumps, while the map
frame "is not continuous" and a pose in it "can change in discrete jumps
at any time". **A controller that differentiates a map-frame pose to
obtain velocity is broken by specification.** Name the frame each pose
argument is expected in, encode it in the type where the language allows,
and document which computations tolerate a jump.

Every input sample carries its own timestamp, and every step takes the
elapsed interval as an argument. On a middleware whose executor processes
callbacks in a round-robin over a cached ready set rather than in arrival
order, a library cannot assume it is called at a fixed rate or once per
input. The ROS 2 documentation says plainly that its default executors
"are not suitable for real-time applications", naming priority inversion
where lower-priority callbacks block higher-priority ones.

## Interface shape

Three features from the nav2 plugin interfaces are worth adopting
verbatim, whatever the middleware:

- **A cooperative cancellation token passed into any long-running call**,
  so a planner can be told to stop without being killed.
- **An externally settable speed limit** that is not a planner parameter,
  because the integrator's protective-field logic needs to command it.
- **An explicit reset distinct from cleanup**, so a controller can be
  returned to a known state without being rebuilt.

Separate construction from configuration from activation from stepping,
mirroring a managed-node lifecycle without importing one. Allocation
happens at configure time. A library that allocates on the first step does
not drop into a real-time system; one that separates the phases does.

**Ship a typed failure taxonomy**, closed and documented. "Returned no
path" is not a diagnosis. The nav2 enumerations are a good model: start
occupied, goal occupied, start or goal outside map bounds, no valid path
found, timed out, cancelled for a planner; no valid control, failed to
make progress, patience exceeded, invalid path, timed out for a
controller. Every reason should be reachable in the test suite.

Do not own the clock, the logger, or the thread. Take a time value as an
argument and a sink for diagnostics.

## Invariants

Assert these. Each belongs in a debug assertion plus a property-based or
metamorphic test, and in a release check with a typed error where the cost
allows. Holzmann's rule applies: side-effect free Boolean tests, and a
failing assertion takes an explicit recovery action.

### Planners

- A returned path is collision-free **under the same map object that was
  passed in**, re-checked at a validity resolution at least as fine as the
  planning resolution, with that resolution recorded alongside the path.
  A coarse motion validator can miss an invalid state between two valid
  ones, so the claim needs its qualifier.
- Consecutive path states are connected by a motion the validity checker
  accepts. This catches stitching bugs in smoothing, which is where most
  of them live.
- Simplification and smoothing never turn a valid path invalid and never
  increase cost.
- Adding an obstacle that does not intersect the returned path leaves that
  path valid.
- Removing an obstacle never increases optimal cost; adding one never
  decreases it.
- Translating and rotating the map, the start, and the goal together
  transforms the returned path by the same amount, modulo seed and
  discretization. This catches frame and origin errors.
- For a graph search with an admissible heuristic, the returned cost
  equals the cost from an uninformed search on the same graph. Differential
  testing against Dijkstra is the strongest cheap oracle available.
- For an anytime planner, the incumbent cost is monotonically
  non-increasing across iterations. Where the algorithm is asymptotically
  near-optimal rather than optimal, the test is a bounded-suboptimality
  band, not convergence.
- The planner terminates. An iteration budget and a wall-clock budget,
  with exceeding either a typed failure rather than a longer wait.
- The same seed, map, and query produce the same path. Determinism is what
  makes every test above reproducible and makes record and replay work.

### Controllers

- Every returned control is inside the configured limit box, on every
  component, including a fallback or a zero. Assert once, on the way out,
  in one place.
- Every returned control is reachable from the previous one under the
  configured rate limit, given the interval actually passed in.
- The elapsed interval is finite, strictly positive, and inside a
  configured band. A negative interval from a clock jump or a replayed log
  is a typed error, not a division.
- A nominal control is never produced from input that failed validation.
- A curvature command is inside the platform's minimum-turning-radius
  bound. A curvature the vehicle cannot execute is a wrong output, not a
  saturated one.
- For a receding-horizon controller reporting a feasible solution, the
  terminal state satisfies the terminal constraint. Accepting a suboptimal
  solution depends on it.
- The solver exit status is propagated, never collapsed to success or
  failure.
- Adding a constant offset to all timestamps does not change the output,
  which catches absolute-time dependencies that break on replay.

### Maps

- **Unknown is not free.** Occupancy carries three semantic classes, and
  code treating unknown as traversable says so explicitly. The integer
  encodings are not standardized across stacks, so define a closed
  enumeration and convert at the boundary rather than passing integers
  around.
- Index and world coordinates round-trip for every in-bounds cell, under
  the origin convention the metadata declares.
- Every index passed to an accessor is bounds-checked, or the accessor is
  named unsafe and documents its precondition.
- Resolution, extent, and origin are immutable for a map object's
  lifetime, and the object carries a version or content hash. That hash is
  what makes "collision-free under the same map" a checkable claim.
- Inflation is idempotent and monotone.
- Graph edges have existing endpoints and finite non-negative costs. A
  negative or non-finite edge cost breaks a shortest-path guarantee
  silently, which is the worst failure mode available.
- **The footprint used for collision checking and the footprint used to
  derive the inflation radius are the same object.** Two sources of truth
  here is the standard cause of a robot planning through a gap it cannot
  fit.

### Kinematics

- Forward and inverse kinematics round-trip within a declared tolerance,
  for every returned branch.
- Every returned solution is inside joint limits, filtered before return.
- **Near a singularity, either the solution is rejected or the resulting
  joint velocity is bounded.** ISO 10218-1 defines singularity as the rank
  of the Jacobian falling below full, and notes that joint velocity can
  become infinite while maintaining Cartesian velocity, producing axis
  speeds an operator does not expect. Assert a floor on the minimum
  singular value or the condition number, expose the threshold, and fail
  loudly rather than returning an enormous joint command. This is the one
  invariant here with direct standards backing.
- The Jacobian matches a numerical differentiation of forward kinematics
  at sampled configurations. This catches analytic-derivative sign errors,
  which are otherwise invisible until the arm moves.
- Rotation representations stay normalized on every return, and the Euler
  convention is fixed and named.
- Angles are wrapped exactly once, and any angular difference used in a
  control law is the wrapped difference.

## Verification

**Metamorphic testing is the highest-value technique available here,**
because every algorithm in a navigation stack has the oracle problem: you
cannot say what the optimal path is, but you can say how the output must
change when the input changes in a known way. Most of the planner and
controller invariants above are metamorphic relations, and they are cheap
against a grid or a graph.

Property-based testing earns its place on the stateful APIs specifically,
since the interesting defects are sequences (configure, step, stale input,
step, reset, step) rather than single calls. Use a state-machine strategy
rather than independent value generation.

Scenario-based evaluation works at three levels of abstraction: a
**functional** scenario in natural language, a **logical** scenario
parameterized with ranges, and a **concrete** scenario with one assignment
that executes. Search-based generation optimizes a surrogate for
criticality, so the scenarios it finds approximate the critical set rather
than covering it, which is worth remembering before claiming coverage.

Two findings from an industrial adoption of simulation-based test
generation are worth carrying. Integration succeeded because a test
interface let the generator drive the stack as a black box without
modifying it. And engineers rated **re-execution for regression** above
finding new failures as the top use case, with the friction being the
manual work of turning a real-world failure into a seed.

For benchmarking, record per run whether a solution was found and whether
it is approximate, its length, smoothness, clearance, segment count, the
time and memory used, and the fraction of motion checks that were valid;
and record per experiment the time and memory limits, the host, the date,
and **the random seed**. Without the seed the rest is not reproducible.

Record and replay belongs at the library's own API boundary, not at the
middleware's. Capture the map snapshot or its version hash, the start, the
goal, the current state, the parameter set, the timestamp, and the seed.
An API-level trace replays without a simulator and survives refactoring of
the middleware wrapper.

## The boundary

What a library owns:

- Numeric limits on its own outputs: a saturation box, a rate limit, a
  curvature bound, a joint-limit filter.
- The envelope it planned against: the footprint, the inflation radius,
  the map version, the validity-check resolution. It knows these exactly
  and nobody else does.
- Its own termination, under an iteration budget and a time budget.
- Admissibility of its inputs, including the windowed validity state.
- Determinism: a seeded generator, no hidden clock read, no hidden global
  state, a replayable input trace.
- Its failure taxonomy, closed and documented.
- Its assumption set and its integration requirements.

What a library cannot own, and must not claim:

- **Emergency stop.** It cannot remove power, cannot guarantee it is
  scheduled, and cannot override the code calling it.
- **Any performance level or integrity level.**
- **Personnel detection and protective fields.** A path planner is not a
  protective device and its collision checking is not personnel detection,
  even when both read the same sensor.
- **Watchdog supervision of itself.**
- **The hazard analysis, the operational design domain, and the acceptance
  criteria.** It may state an assumed criterion; only the integrator
  validates it.
- **The minimal risk condition and the fallback maneuver.** Independence
  is the point, and a second planner inside the same library is not
  independent.
- **Mode management and enable authority.** The library consumes the
  resulting permission as an input.

The artifact that carries this is one document per algorithm: assumptions
of use, inputs and their required validity, limits enforced, failure modes
returned, and a numbered list of integration requirements. That document
is also the spec its tests reference, which fits the traceability practice
in [workflow/sdd.md](../workflow/sdd.md) without a parallel process.

## Sources

- ISO 3691-4:2023, Industrial trucks, safety requirements and verification, part 4. https://www.iso.org/standard/83545.html
- ISO 10218-1:2025 and ISO 10218-2:2025, Robotics, safety requirements. https://www.iso.org/standard/73933.html and https://www.iso.org/standard/73934.html
- ISO 13849-1:2023, Safety-related parts of control systems. https://www.iso.org/standard/73481.html
- IEC 62061:2021, Functional safety of safety-related control systems. https://webstore.iec.ch/en/publication/59927
- ISO 21448:2022, Safety of the intended functionality. https://www.iso.org/standard/77490.html
- ISO 34502:2022, Scenario-based safety evaluation framework. https://www.iso.org/standard/78951.html
- ISO 13850:2015, Emergency stop function. https://www.iso.org/standard/59970.html
- Marvel and Norcross, "Implementing speed and separation monitoring in collaborative robot workcells", Robotics and Computer-Integrated Manufacturing 44, 2017. https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=914783
- Ammann and colleagues, "Identification and Evaluation of Potential Functional Insufficiencies of an MPC-based Trajectory Planner". https://arxiv.org/pdf/2407.21569
- AUTOSAR E2E Protocol Specification, R22-11. https://www.autosar.org/fileadmin/standards/R22-11/FO/AUTOSAR_PRS_E2EProtocol.pdf
- Linux watchdog API. https://www.kernel.org/doc/html/latest/watchdog/watchdog-api.html
- systemd `sd_watchdog_enabled`. https://www.freedesktop.org/software/systemd/man/sd_watchdog_enabled.html
- REP 103, Standard Units of Measure and Coordinate Conventions. https://www.ros.org/reps/rep-0103.html
- REP 105, Coordinate Frames for Mobile Platforms. https://www.ros.org/reps/rep-0105.html
- REP 2004, Package Quality Categories. https://www.ros.org/reps/rep-2004.html
- ROS 2, About Executors. https://docs.ros.org/en/jazzy/Concepts/Intermediate/About-Executors.html
- Casini, Blass, Lutkebohle and Brandenburg, "Response-Time Analysis of ROS 2 Processing Chains", ECRTS 2019. https://drops.dagstuhl.de/storage/00lipics/lipics-vol133-ecrts2019/LIPIcs.ECRTS.2019.6/LIPIcs.ECRTS.2019.6.pdf
- nav2 planner and controller interfaces. https://github.com/ros-navigation/navigation2/tree/main/nav2_core/include/nav2_core
- Santos, Cunha and Macedo, "Property-Based Testing for the Robot Operating System", A-TEST 2018. http://haslab.github.io/SAFER/a-test18.pdf
- "Finding Critical Scenarios for Automated Driving Systems: A Systematic Literature Review". https://arxiv.org/pdf/2110.08664
- OMPL benchmarking. https://ompl.kavrakilab.org/benchmark.html
- Karaman and Frazzoli, "Sampling-based Algorithms for Optimal Motion Planning". https://arxiv.org/abs/1105.1186
- Li, Littlefield and Bekris, "Asymptotically Optimal Sampling-based Kinodynamic Planning". https://arxiv.org/abs/1407.2896
