# Review and merge

## Three constraint layers

Three independent layers keep agent-assisted work safe, each catching what
the others miss:

| Layer | Mechanism | Purpose |
|---|---|---|
| 1, guidelines | Thin agent-instruction files pointing to this library / the project's `CONTRIBUTING.md` | Same rules for humans and agents; no separate, weaker standard for AI-authored work |
| 2, automation | CI/CD quality gates (build, tests, coverage, lint) | Blocks broken builds and regressions mechanically, not by trusting review to catch everything |
| 3, review | Manual merge, human judgment | Validates intent and architecture fit that automation cannot check |

## Human-only merge

Agents open and update pull requests; they do not merge them, ever,
regardless of how confident the automated checks look. A human approves
and merges. This is a hard rule, not a suggestion for cautious cases.

An agent's job ends at "PR is open, CI is green, here is the evidence." It
does not end at "merged."

## No weakening gates to make CI green

If a quality gate is failing, fix the underlying problem. Do not lower a
coverage threshold, comment out a failing test, add a blanket `# noqa`, or
otherwise weaken the gate to get a green checkmark. If a gate is genuinely
wrong for a specific case, that is a `docs/decisions.md` entry proposing the
change, reviewed like any other spec change, not a silent edit slipped into
the same PR that needed it to pass.

## PR requirements

- Description links the originating issue and spec.
- A checklist mapping to the spec's acceptance criteria, with items checked
  only once actually verified (not aspirationally, before running
  anything).
- For UI changes: visual evidence (screenshots or a recorded pass) attached
  to the PR, not just claimed in the description.
- CI green before requesting merge.

## Evidence, not claims

Do not report a task as done, a bug as fixed, or a test as passing without
attaching the evidence (a command's actual output, a screenshot, a test
report), not a description of what should have happened. Scale the amount
of evidence to the blast radius of the change: a one-line typo fix needs
none of this; a change touching a hot path, hardware interface, or public
API needs a full run's output attached.

This extends to demonstrations: a screenshot or recording must reflect
real, unedited output. Do not composite images, fake data when a live
source is empty, or otherwise make something "look correct" that is not
backed by real behavior. Treat "looks right in the screenshot" without the
underlying behavior actually being correct as a failed task, not a
workaround. See [agents/claude.md](../agents/claude.md) for the full
no-fabricated-evidence rule.

## Merge policy

- **Rebase and merge**, keeping history linear. Squash only when a
  feature branch's intermediate commits are genuinely not worth preserving
  (e.g. "wip", "fix typo" churn). Prefer atomic commits from the start so
  squashing is not necessary.
- No force-push to `main`.
