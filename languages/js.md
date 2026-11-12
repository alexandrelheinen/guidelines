# JavaScript

Two valid patterns are in active use, depending on project type: pick the
one that matches what you are building, do not default to the app-style
tooling for a static site.

## App-style projects (bundled, tested, monorepo-friendly)

- Flat ESLint config (`eslint.config.mjs`) shared from a single package in
  a monorepo, re-exported by each workspace rather than duplicated.
- Prettier: `semi: true`, `singleQuote: true`, `trailingComma: "all"`,
  `printWidth: 100`, `tabWidth: 2`.
- React is the usual UI library for app-style work in this family. Keep
  guidance at that level here: hooks, components, and the React ESLint
  plugins below. Framework-specific advice (Next.js routing, App Router,
  or a Next-only bundler setup) belongs in the project or in an official
  stack skill, and must not override a non-Next project's chosen bundler
  (CRA, Vite, Parcel, or none).
- `eslint-plugin-react` + `eslint-plugin-react-hooks` recommended rules
  where React is in use.
- Prefer a version-synced official React or framework skill, pinned
  in-repo, over inventing parallel UI guidance here. See
  [agents/skills.md](../agents/skills.md).
- CI runs lint with zero tolerance: `--max-warnings 0`.
- Workspace-internal imports use a consistent scope prefix
  (`@scope/package-name`), never relative paths that reach across package
  boundaries (`../../other-package/src/...`).

## Static-site / no-bundler projects

- Vanilla JS (or a small library already loaded, like D3), no bundler:
  `<script>` tags or inline scripts in templates is the established
  pattern, not a shortcut to replace later.
- Do not introduce a Node/webpack build step for a static site without an
  explicit spec and sign-off: it changes the deploy model for everyone
  touching the project afterward.
- Client-side behavior must degrade gracefully if JS fails to load; the
  content must still be readable without it.

## Shared, regardless of project type

- Prefix intentionally unused parameters with `_`, enforced via ESLint's
  `argsIgnorePattern: '^_'` where a linter is present (see
  [style/naming.md](../style/naming.md#intentionally-unused-parameters)).
- `camelCase` for variables and functions, `PascalCase` for
  classes/components, `UPPER_CASE` for module-level constants.
