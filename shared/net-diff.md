# The net branch diff

How a skill working from a branch gathers what it describes. Pointed at, never
copied.

- The unit is the net diff vs the parent branch: not the chat session, and not
  the commits that got there.
- Run all four fresh every time, even when the diff seems unchanged:

```
git fetch origin <base>
git log --oneline origin/<base>...HEAD
git diff --stat origin/<base>...HEAD
git diff origin/<base>...HEAD
```

- `<base>` is the connected PR's `baseRefName` where there is one
  (`gh pr view --json baseRefName`), and otherwise the repo's default branch
  (`master`, else `main`).
- Compare against `origin/<base>`, never the local branch. A local base that
  hasn't been pulled since something merged puts that merged work in the diff
  as if this branch made it.
- Net end state only. Intermediate churn doesn't count, so a file added and then
  deleted on the branch is excluded.
- Read the full diff, not just `--stat`, and keep the per-file counts to hand
  where sizing matters.
- Skip lockfiles, generated files and vendored output.
- Claim only what the diff shows. Don't invent behaviour that isn't in it.
