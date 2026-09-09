# Comments

Two stances coexist across current projects, chosen by language and by how
public/stable the surface is. Pick per-project, but pick deliberately and
say so in the project's own guideline file — don't mix silently.

## Default philosophy

Code should be self-explanatory through naming and structure. Comment only
non-obvious logic, business rules, or a constraint that isn't visible from
the code itself (a workaround for a specific bug, a hidden invariant, why an
optimization exists). Don't narrate what the code already says.

This is the default for Python application code, JS/TS, and Rust, and it
governs private/internal C++ functions even in projects that require
Doxygen on public APIs (see below).

## Public API documentation — required for library/module boundaries

For any public API surface — a public C++ header, a published Python
package, a shared library used by other projects — documentation is
mandatory, not optional, because the reader can't fall back on reading the
implementation.

**C / C++**: Doxygen `/** */` blocks on every public class, function, and
method. Required tags:
- `@brief` — one line
- `@param` — one per parameter
- `@return` — for any non-void return
- `@throws` — for any exception the caller must handle

**Python**: Google-style docstrings on every public module, class, and
function. Required sections when applicable: `Args`, `Returns`, `Raises`,
`Yields`. A one-line docstring is enough for anything whose behavior is
fully captured by its name and signature; use the full section format when
there's more to say.

```python
def haversine_distance(lat1: float, lon1: float, lat2: float, lon2: float) -> float:
    """Great-circle distance between two WGS84 points, in meters.

    Args:
        lat1: Latitude of the first point, in degrees.
        lon1: Longitude of the first point, in degrees.
        lat2: Latitude of the second point, in degrees.
        lon2: Longitude of the second point, in degrees.

    Returns:
        Distance in meters.
    """
```

**Rust**: `///` doc comments on every public item, following the same
brief/params/returns shape as the above where relevant to the type.

## What never needs a comment

- A test's `describe`/`it` or function name, if it already states the
  behavior being verified — don't add a comment above it repeating the name.
- A type hint or the parameter list itself — don't restate types in prose.
- The obvious control flow (`# loop over sensors` above a `for sensor in
  sensors:` is noise).

## `any`/escape-hatch justification

Any use of a type-system escape hatch (`any` in TypeScript, `# type: ignore`
in Python, `unsafe` in Rust/C++) requires a one-line comment immediately
above it explaining why it's necessary. This is the one case where a
comment is mandatory regardless of the project's general comment
philosophy.
