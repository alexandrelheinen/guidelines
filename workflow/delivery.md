# Delivery

A merged change still has to reach a place where it runs. The V-cycle
in [integration.md](integration.md) ends at merge; this file starts
there. The cost of a bad release picks the practice below. A release you
can roll back and a release that moves a machine are different costs, so
they call for different practices. The people who write the software and
the people who deploy it need one artifact they can both name.

## Three practices

The three names describe different places where a person still decides.

| Practice | What runs unattended | What a person still decides |
|---|---|---|
| Continuous integration | Every change is built and tested, including its merge with the target branch | Whether the result is releasable, and when it deploys |
| Continuous delivery | Every change that passes produces a versioned artifact that could be deployed | When that artifact deploys |
| Continuous deployment | A change that passes is deployed, with no separate approval | The policy that allows that, and the response when the running system disagrees |

A library or a service whose failure is a rollback can sit on
continuous deployment. A system whose failure is a wrong command to a
machine sits on continuous delivery. The artifact is always ready, and
a person decides when it reaches the machine.

## Software that moves a physical machine

Software that moves a machine keeps a human approval in front of field
deployment. An automatic deployment is allowed only when the tests
justify it. The suite has to cover the failures that would make an
unattended deploy unsafe, and those tests have to be the ones that
passed on the artifact being deployed. A green build of a different
commit is a different fact.

Field approval sits after the verification a module earns in
[criticality.md](criticality.md), and after the split in
[domains/robotics.md](../domains/robotics.md) between what a library
owns and what only the integrating system can own. It does not replace
either.

## The sequence

Three steps, in this order, on every change that ships:

1. Continuous integration runs on the change.
2. That run produces a versioned artifact.
3. Approval releases that artifact. The approval is a person, or a
   policy the tests justify, as [above](#software-that-moves-a-physical-machine).

The versioned artifact is the shared reference between the people who
write the software and the people who deploy it. Both sides name that
artifact. Deployers ship that artifact. A branch tip is a moving
target, and a local rebuild is a different build. Developers cite the
version when they say what was released.

## The loop

Delivery sits inside a loop that continues after the deploy:

plan, code, build, test, release, deploy, operate, monitor.

Monitoring feeds the next plan. An alert, a field report, or a drift
between the artifact and the behavior operators see becomes the next
issue, and that issue is the working contract for the change. See
[sdd.md](sdd.md#the-issue-is-the-working-contract).

## The pipeline

The pipeline is a versioned file at the repository root. It changes
only through a pull request. The CI server's own UI holds no pipeline
configuration. A setting that exists only in that UI has no diff, so
review cannot see it, and it will drift from the file.

A host that requires its pipeline file in a fixed directory still
stores that file in the repository, and the file still changes only
through a pull request. The UI stays free of pipeline configuration
either way.

CI runs on the merge result of the change with the target branch, and
that run is a required check. A build of the branch alone misses a
failure that appears only once the change is combined with the target
branch. The required check is that merge result.

The same pipeline runs security checks on every change, including the
first push, so a release job is not the first time they run:

- leaked secrets
- dependencies with known weaknesses
- insecure code

Unauthorized access to the repository or to CI is a failed
delivery. Artifacts produced while that access was open are untrusted.
Revoke the credential that was exposed, and re-run the pipeline from
the versioned file. See [Credentials](#credentials).

## Credentials

One named, revocable token per machine. The name identifies the
machine, so revoking the token removes that machine's access and leaves
every other machine alone.

Store the token in the operating system keychain or in a vault. A
versioned file is the wrong place, and so is a local config file,
including an untracked env file. A file on disk is a copy that review
cannot see and that a backup will keep.

Scripts do not print the token. See
[languages/sh.md](../languages/sh.md#never-echo-secrets) and
[style/errors.md](../style/errors.md#logging).
