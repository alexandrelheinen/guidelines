# TypeScript

Builds on [js.md](js.md): same ESLint/Prettier baseline, plus the
following TypeScript-specific rules. Applies to app-style projects; a
static site staying vanilla-JS has no reason to introduce TypeScript
solely for these rules.

## Compiler settings

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "isolatedModules": true,
    "noEmit": true,
    "declaration": true,
    "declarationMap": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

`strict: true` is non-negotiable for new projects: enabling it later, once
a codebase has grown, is far more painful than starting with it on.

## The `any` escape hatch

No un-justified `any`. Every use requires a one-line comment explaining
why a more specific type is not feasible (see
[style/comments.md](../style/comments.md#anyescape-hatch-justification)).
Prefer `unknown` with a narrowing check over `any` wherever the value's
shape is genuinely unknown at that point.

## Validation at the boundary

Validate all external input (API request bodies, environment variables,
data crossing a serialization boundary) with a runtime schema library
(e.g. Zod) rather than trusting a TypeScript type alone: types disappear
at runtime, so anything crossing a boundary needs a real check. See
[style/errors.md](../style/errors.md#validate-at-boundaries-trust-internals).

## Testing

Co-locate `foo.test.ts` next to `foo.ts` for app-style projects, using
`describe`/`it` blocks. Test names should read as behavior statements,
not restate the function name (see
[style/naming.md](../style/naming.md#test-naming)).
