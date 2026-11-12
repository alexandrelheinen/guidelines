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
*what*: the diff already shows what changed. That *why* is the argument
from [agents/writing.md](../agents/writing.md#argument). The subject
names the claim, and the body gives the warrant and the grounds. A body
that restates the diff is a list of facts.

**Plain imperative-mood subjects** (no typed prefix) are an accepted
alternative for content-heavy or documentation-first repos, where most
commits are prose changes and a `docs:`/`content:` prefix on every single
commit adds noise without adding information. Pick one style per project
and apply it consistently: do not mix typed and untyped commits in the
same repo.

## Atomic, reviewable commits

One logical change per commit, traceable to a single item in the issue's
acceptance-criteria checklist. Prefer several small, clearly-typed commits
within a PR over one large squashed commit: it makes `git bisect` and
review both easier, and a bad commit can be reverted without taking
unrelated changes with it.

### Agents must split the work

When an agent (or a human directing one) lands a multi-part change, it
**must** produce a **readable, reviewable commit series**, not a single
omnibus commit, unless the maintainer explicitly asks to squash.

| Do | Do not |
| --- | --- |
| One concern per commit (docs, then commands, then skills, then pins) | Mix unrelated trees in one commit |
| Order commits so a reviewer can stop after any prefix and still understand the story | Depend on later commits to make earlier ones compile when that can be avoided |
| Keep each commit message's subject as the claim and the body as the why | Restate the file list in the body |
| Match commit boundaries to acceptance-criteria slices when a spec exists | Hide a process change inside a large feature dump |

A PR that is easier to review as five commits than as one diff is the
default shape for agent-authored work in this family.

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
or generated artifacts that belong in `.gitignore` instead. How a
credential is issued and where it is stored is in
[delivery.md](delivery.md#credentials). The rule is one named, revocable
token per machine, in the operating system keychain or a vault.
