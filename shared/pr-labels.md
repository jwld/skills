# PR stage labels

The `[PR]` labels that track a PR through review, and how a skill moves one.
Pointed at, never copied.

Only in the `propertywerks` GitHub org. Check the remote owner, and skip every
label step anywhere else.

## Stages

In the order a PR moves through them, each with the `write-up-branch`
audience it maps to:

- `[PR] in progress` - historical reference
- `[PR] needs review` - human review
- `[PR] in review`, `[PR] changes requested`, `[PR] needs re-review`,
  `[PR] needs final pass` and `[PR] in final pass` - human review
- `[PR] ready for merge` - historical reference

A PR already carrying one takes its write-up audience from it, so a later pass
reads the label rather than asking. The repo's `docs/conventions/github.md`
sets the reviewer and assignee a PR opens with.

## Moving a PR on

A PR carries one stage label at a time. Swap the new one in for whichever
`[PR]` label it carries now (`gh pr view --json labels`):

```bash
gh pr edit --add-label "<new stage>" --remove-label "<current stage>"
```
