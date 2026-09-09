# C#

Thin — current usage is one project's host/interop layer, supplemented
here with standard .NET conventions for anything not yet established
locally.

## Project settings

`Directory.Build.props` at the repo root, applied to every C# project:

```xml
<Project>
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    <Deterministic>true</Deterministic>
    <LangVersion>latest</LangVersion>
  </PropertyGroup>
</Project>
```

`TreatWarningsAsErrors` is deliberate: it turns the compiler into a
quality gate instead of a source of ignorable noise.

## Nullability

With `Nullable enable`, avoid `null!` suppression except at proven
boundaries (deserialization, interop with unmanaged code) — and comment
why at each use, per
[style/comments.md](../style/comments.md#anyescape-hatch-justification).

## Error handling

Constructors and factory methods validate arguments and throw immediately
(`ArgumentOutOfRangeException`, `ArgumentNullException`) rather than
constructing an object in an invalid state. For resource contracts under
backpressure (a bounded queue, a channel), model the degradation policy as
an explicit enum with a loss-accounting field rather than silently
dropping data:

```csharp
public enum FullMode { Wait, DropOldest, DropWrite }
// expose a DroppedCount so callers can observe loss, not just guess at it
```

## Testing

xUnit. Test method naming:
`MethodUnderTest_Scenario_ExpectedBehavior` — see
[style/naming.md](../style/naming.md#test-naming).

## Naming

Standard .NET conventions: `PascalCase` for types, methods, and public
properties; `camelCase` for local variables and parameters; `_camelCase`
for private fields. This differs from the C/C++/Python `snake_case`
default in [style/naming.md](../style/naming.md) — follow the ecosystem
convention for whichever language is in use, don't force C# into
`snake_case`.
