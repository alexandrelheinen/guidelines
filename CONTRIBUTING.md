# Contributing

This repository is a single-owner shared guidelines library, not a project
seeking outside contributors, but it still needs a documented process
since other repositories depend on it as a git submodule.

## Who reviews changes

Anyone may open an issue or a pull request. The repository owner reviews
and merges; there is no separate contributor base to coordinate with.

## How a guideline changes

- A change to any prose in this repository follows
  [agents/writing.md](agents/writing.md): run its self-review checklist
  before opening a pull request.
- A new file follows [style/naming.md](style/naming.md): one word, or a
  language extension, matching the pattern of its directory.
- A change that would contradict a per-project override already noted in a
  `languages/*.md` file should update that note rather than silently
  removing it.

## Versioning

This repository is versioned with git tags (`vX.Y.Z`). The scheme is
semantic in spirit rather than strict SemVer, since what changes here is
prose, not an API:

- **Major** (`v2.0.0`): a file rename or removal that a consuming project
  might reference directly, since it breaks a link or a Claude Code
  `@import`, or a change to the license.
- **Minor** (`vX.1.0`): a new file or a new section a consuming project can
  adopt without anything breaking.
- **Patch** (`vX.Y.1`): a wording fix or a clarification that does not
  change the substance of a rule.

Every tagged release gets a matching entry in
[CHANGELOG.md](CHANGELOG.md).

## Consuming a specific version

A project that wants stability over freshness can pin its submodule to a
tag instead of tracking `main`:

```bash
cd guidelines
git checkout v1.0.0
cd ..
git add guidelines
git commit -m "Pin guidelines to v1.0.0"
```

Track `main`, as the README describes, only once a project is comfortable
absorbing a guideline change as soon as it lands here.

## Documenting a local override

A consuming project will sometimes deviate from a rule here on purpose,
for example a Python line length of 79 instead of 88. Document the
deviation in that project's own `CONTRIBUTING.md` or `docs/guidelines.md`,
under a heading naming the rule and the reason, rather than diverging
silently. See [agents/context.md](agents/context.md) for what belongs in a
project's own files versus in this library.
