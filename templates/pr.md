## Summary

What changed and why (1-3 bullets): link the issue and spec this
implements.

## Acceptance criteria

Checklist mirrored from the linked issue/spec. Check an item only once
it is actually verified, not aspirationally:

- [ ] `<ID>`:
- [ ] `<ID>`:

## Test plan

- [ ] New/updated tests cover the acceptance criteria above (see
      `workflow/tdd.md`)
- [ ] Full local validation script passes (`./scripts/validate.sh` or
      equivalent)
- [ ] Linter / formatter / type-checker clean
- [ ] Coverage gate met

## Evidence

Attach the actual output that backs the checklist above: command output,
screenshots, or a recording: scaled to the change's blast radius (see
`agents/claude.md`'s evidence tiers). Do not just assert the checks passed.

## Screenshots (if UI changes)

Before/after, or a link to the visual-inspection artifact.

## Checklist

- [ ] No secrets committed
- [ ] Docs updated if behavior changed
- [ ] Rebased onto latest `main`, no conflicts
- [ ] CI green
