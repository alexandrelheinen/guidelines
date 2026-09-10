# Review and merge

## Three constraint layers

Three independent layers keep agent-assisted work safe, each catching what
the others miss:

| Layer | Mechanism | Purpose |
|---|---|---|
| 1, guidelines | Thin agent-instruction files pointing to this library / the project's `CONTRIBUTING.md` | Same rules for humans and agents; no separate, weaker standard for AI-authored work |
| 2, automation | CI/CD quality gates (build, tests, coverage, lint) | Blocks broken builds and regressions mechanically, not by trusting review to catch everything |
| 3, review | Manual merge, human judgment | Validates intent and architecture fit that automation cannot check |

## Running a review

Review a diff along two independent axes, because they fail differently:

- **Standards**: does the code follow what this project documents, meaning
  the language file in this library plus whatever the project adds on top?
- **Spec**: does the code do what the originating spec asked for, including
  the acceptance criteria it did not implement?

Running the two separately keeps a reviewer from talking itself out of a
missing requirement because the code it is reading is tidy. The
`code-review` skill in the mattpocock skillset does exactly this, in
parallel subagents, and it is the preferred mechanism where it is
installed: it reads the project's documented standards rather than
asserting its own, which is the kind of skill worth reaching for. See
[integrations/toolkits.md](../integrations/toolkits.md#the-governing-principle).

The mechanism is free to change. What does not change is that both axes get
covered and that the reviewer is not the same context that wrote the code.

## Human-only merge

Agents open and update pull requests; they do not merge them, ever,
regardless of how confident the automated checks look. A human approves
and merges. This is a hard rule, and it holds no matter how many review
subagents signed off first: an automated reviewer is a mechanism inside
layer 2, not a substitute for layer 3.

The one exception is a maintainer who asks for a merge directly in the
active task, which is the maintainer exercising layer 3 rather than an
agent bypassing it.

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
