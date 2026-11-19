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

- `<base>` is the branch's base, as [base-branch.md](base-branch.md) sets out.
- Net end state only. Intermediate churn doesn't count, so a file added and then
  deleted on the branch is excluded.
- Read the full diff, not just `--stat`, and keep the per-file counts to hand
  where sizing matters.
- Skip lockfiles, generated files and vendored output.
- Claim only what the diff shows. Don't invent behaviour that isn't in it.
