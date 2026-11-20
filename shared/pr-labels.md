# PR labels

The `[PR]` labels, their write-up audiences, and how a skill moves a review
stage. Pointed at, never copied.

Only in the `propertywerks` GitHub org. Check the remote owner, and skip every
label step anywhere else.

A PR already carrying one of the labels below takes its write-up audience
from it, so a later pass reads the label rather than asking.

## Stages

In the order a PR moves through them, each with the `write-up-branch`
audience it maps to:

- `[PR] in progress` - historical reference
- `[PR] needs review` - human review
- `[PR] in review`, `[PR] changes requested`, `[PR] needs re-review`,
  `[PR] needs final pass` and `[PR] in final pass` - human review
- `[PR] ready for merge` - historical reference

The repo's `docs/conventions/github.md` sets the reviewer and assignee a PR
opens with.

## Feature

`[PR] feature` holds the reviewed work so it can merge into master at once.
Local work is a branch stacked into it, never a checkout of that branch.
Write-up audience is leave as is: keep the existing title and body, and skip
`write-up-branch`.

## Moving a PR on

A PR carries one stage label at a time. Swap the new one in for whichever
`[PR]` label it carries now (`gh pr view --json labels`):

```bash
gh pr edit --add-label "<new stage>" --remove-label "<current stage>"
```
