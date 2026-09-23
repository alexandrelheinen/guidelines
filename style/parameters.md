# Parameters

An algorithm has four parts, and they are independent:

| Part | What it is | Where it lives |
| --- | --- | --- |
| **Inputs** | Values that change over time as the system runs | Function arguments, messages, sensor readings |
| **Outputs** | Values produced each step | Return values, published commands, logged fields |
| **Parameters** | Design choices that do not change over a run | Configuration files, loaded at start and refused when absent |
| **Logic** | The procedure that turns inputs and parameters into outputs | Source code |

Mixing them is how a tuned number becomes invisible, how two copies of the
same bound drift apart, and how a comment about "0.30 s" lies after the
YAML moved.

## What counts as a parameter

Anything dimensional, timed, weighted, or counted that a designer chose so
the algorithm behaves a certain way:

- Speeds, accelerations, distances, clearances, angles, durations
- Tolerances and margins that define arrival, association, or trust
- Iteration counts and sample densities chosen for a numerical method
- Gains, lead times, and profile switches

Those values are named in configuration. Source code reads them through a
typed settings object. A missing key fails at load naming itself. A
default buried in Python (or Rust, or C++) is a default nobody reviews.

## What may stay in source

Not every literal is a parameter.

- **Unit conversions** that are definitions, not choices: nanoseconds per
  second, degrees per radian.
- **Exact algebraic coefficients** of a named basis or identity: Hermite
  weights, `15/8` for a rest-to-rest quintic peak, geometric identities
  such as `5 Z / (2 V)`.
- **Language and architecture thresholds**: floating-point epsilons,
  rounding offsets that keep a projection inside a closed set after
  `hypot`, floors that reject a zero divisor, buffer sizes fixed by a
  protocol.
- **External protocol scales** the code does not own: a gripper command
  range declared by a vendor model.

A comment on those may explain the algebra or the numerical reason. It
must not pretend they are tunable from configuration.

## Comments do not restate parameters

Comments and docstrings describe logic, invariants, and why a structure
exists. They do not quote a shipped configuration value, and they do not
claim what happens at a specific YAML number.

Wrong:

```python
# 0.30 s is one and a half times the measured decorrelation time.
# At 1.00 m/s the traverse finishes in under a second.
```

Right:

```python
# Drift is carried only over the configured horizon; longer horizons are
# not a prediction on this belt.
# Speed and acceleration ceilings come from motion settings.
```

Provenance for a particular number (measured, assumed, taken from a
datasheet) belongs beside that number in the configuration file or in a
measurements document, not in the implementation that reads it.

## One source of truth

When the same physical bound appears in more than one place (a reach
annulus, a belt width, a servo gain), every consumer reads the same
configuration key. Duplicating the literal in a second module, even with
the same digits, is a defect: the copies will diverge.

## Tests

A test may hardcode inputs and expected outputs. It may also hardcode
parameters when it is proving an algorithm in isolation, provided the
production path still loads those parameters from configuration. A test
that asserts "the shipped YAML still says X" belongs in a config-load
test, not scattered through the algorithm suite.
