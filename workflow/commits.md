# Commits

## Format

**Conventional Commits** is the recommended default for code-focused
projects:

```
<type>(<scope>): <imperative subject>

<body>

<footer>
```

Types: `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `build`, `ci`,
`chore`, `style`, `revert`. Subject in imperative mood (`Add`, `Fix`,
`Update`, not `Added`/`Fixes`), no trailing period, ideally under ~72
characters. Body wraps around 72 columns and explains *why*, not just
*what*: the diff already shows what changed.

**Plain imperative-mood subjects** (no typed prefix) are an accepted
alternative for content-heavy or documentation-first repos, where most
commits are prose changes and a `docs:`/`content:` prefix on every single
commit adds noise without adding information. Pick one style per project
and apply it consistently: do not mix typed and untyped commits in the
same repo.

## Atomic commits

One logical change per commit, traceable to a single item in the issue's
acceptance-criteria checklist. Prefer several small, clearly-typed commits
within a PR over one large squashed commit: it makes `git bisect` and
review both easier, and a bad commit can be reverted without taking
unrelated changes with it.

## Trailers

Add a `Co-authored-by:` trailer whenever an AI agent materially
contributed to a commit, naming the human who directed the work:

```
Co-authored-by: Your Name <you@example.com>
```

## Breaking changes

Signal a breaking change with a `!` after the type/scope
(`feat(api)!: remove legacy auth header`) or a `BREAKING CHANGE:` footer;
either is fine, but the change must be flagged one of these two ways, not
buried in the body prose.

## What never goes in a commit

Secrets, `.env` files, build output (`_site/`, `dist/`, `node_modules/`),
or generated artifacts that belong in `.gitignore` instead.
