---
name: teardown-worktree
description: >-
  Tears down a worktree whose PR has merged: removes the worktree and
  force-deletes its local branch. Use when the user is done with a worktree,
  or its PR has merged and they want it gone.
---

# Tear down a worktree

The worktrees, their slots and their windows are as the `new-worktree` skill
sets out ([new-worktree/SKILL.md](../new-worktree/SKILL.md)). Run every
command here from the main checkout, the first path `git worktree list`
prints, since the worktree's folder is about to go.

```
- [ ] 1. Pick the worktree
- [ ] 2. Check the PR merged
- [ ] 3. Check nothing is left behind
- [ ] 4. Remove it
- [ ] 5. Reply
```

### 1. Pick the worktree

The one the user names, else the checkout this chat sits in. Stop when that
is the main checkout: it is never torn down.

Done when you have its `<path>` and `<branch>` (or that it's detached).

### 2. Check the PR merged

```bash
gh pr list --head <branch> --state merged --json number,url,headRefOid --limit 1
```

A squash merge leaves the branch tip off the default branch, so the merged PR
is the proof the work landed, not `git branch --merged`.

When there is no merged PR (still open, never opened, or detached with
commits of its own), say so and put the choice as
[shared/choices.md](../shared/choices.md) sets out: remove the worktree and
keep the branch, or leave it.

Done when the PR is merged or the user has chosen.

### 3. Check nothing is left behind

The force delete in step 4 loses anything the PR doesn't hold. Check both:

- **Uncommitted changes** - `git -C <path> status --short`.
- **Commits after the merge** - `git log --oneline <headRefOid>..<branch>`,
  commits made on the branch after the PR's last push. When git doesn't know
  `<headRefOid>`, `git fetch origin pull/<number>/head` first.

When either shows something, show it and put the choice as
[shared/choices.md](../shared/choices.md) sets out: stop, so it can go into a
new PR, or discard it and carry on.

Done when both are empty or the user chose to discard.

### 4. Remove it

```bash
git worktree remove <path>             # --force once the user chose to discard
git branch -D -- <branch>              # merged PRs only
```

- Ignored files (`node_modules`, the env copies) don't block the remove.
- `-D`, since a squash-merged branch never satisfies `-d`.
- The branch on `origin` stays as GitHub left it.
- The worktree's `_wt<n>` databases stay, and the next worktree in that slot
  resets them at setup.
- A worktree folder deleted by hand leaves a stale entry, which
  `git worktree prune` clears.

Done when `git worktree list` no longer shows `<path>` and, for a merged PR,
`git branch --list <branch>` prints nothing.

### 5. Reply

Say what was removed and whether the branch was deleted or kept, with the
merged PR's link. End with closing that worktree's window, which now points at
a missing folder.
