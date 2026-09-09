# Branching

## Sync before you branch

Always branch from the current tip of `main`, never from a stale local
copy or an old feature branch:

```bash
git fetch --all -p
git checkout main
git pull --rebase origin main
git checkout -b <branch-name>
```

## Naming

`<type>/<slug>`, matching the commit type vocabulary in
[commits.md](commits.md): `feat/add-parser`, `fix/null-check-on-empty-input`,
`ci/governance-job`, `docs/update-readme`.

Cloud/agent-initiated branches carry an additional prefix so they're
identifiable at a glance, e.g. `cursor/<feature>-<hash>` — adopt an
equivalent convention for whichever agent tooling a project uses.

## Keeping a branch current

Rebase an open PR onto `origin/main` before merge if `main` has moved,
rather than merging main into the feature branch. This keeps history
linear (see [review.md § Merge policy](review.md#merge-policy)).

## Protected branches

`main` is protected and typically deploys automatically on push (via
whatever CI/CD is configured for the project). No force-push to `main`,
ever, by anyone — including agents.
