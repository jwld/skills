---
name: teardown
description: >-
  Clears away a branch whose PR has merged: removes its worktree, or switches
  the checkout to the default branch, and deletes the local branch.
disable-model-invocation: true
---

# Teardown

For the branch this chat sits on, or the worktree the user names, once its PR
has merged into the default branch. Where it sits decides the cleanup:

- **A worktree** - not the first path `git worktree list` prints. The
  worktree goes, then the branch. Worktrees, their slots and their windows
  are as the `new-worktree` skill sets out
  ([new-worktree/SKILL.md](../new-worktree/SKILL.md)).
- **The main checkout** - the first path, including a repo with no other
  worktrees. It moves to the default branch, then the branch goes.

`<default>` is the default branch, as
[shared/base-branch.md](../shared/base-branch.md) sets out. Stop when the
checkout is already on it: there is nothing to tear down.

```
- [ ] 1. Check the PR merged
- [ ] 2. Check nothing is left behind
- [ ] 3. Tear it down
- [ ] 4. Reply
```

### 1. Check the PR merged

```bash
gh pr list --head <branch> --state merged --json number,url,headRefOid --limit 1
```

A squash merge leaves the branch tip off the default branch, so the merged PR
is the proof the work landed, not `git branch --merged`.

When there is no merged PR (still open, never opened, or a detached HEAD with
commits of its own), say so and stop. In a worktree, put the choice as
[shared/choices.md](../shared/choices.md) sets out instead: remove the
worktree and keep the branch, or leave it. A detached worktree with no commits
of its own, and nothing uncommitted, is just removed.

Done when the PR is merged, or the user has chosen.

### 2. Check nothing is left behind

The force delete in step 3 loses anything the PR doesn't hold. Check both:

- **Uncommitted changes** - `git -C <path> status --short`.
- **Commits after the merge** - `git log --oneline <headRefOid>..<branch>`,
  commits made on the branch after the PR's last push. When git doesn't know
  `<headRefOid>`, `git fetch origin pull/<number>/head` first.

When either shows something, show it and put the choice as
[shared/choices.md](../shared/choices.md) sets out: stop, so it can go into a
new PR, or discard it and carry on.

Done when both are empty or the user chose to discard.

### 3. Tear it down

**A worktree** - from the main checkout, since the worktree's folder is about
to go:

```bash
git worktree remove <path>             # --force once the user chose to discard
git branch -D -- <branch>              # merged PRs only
```

- Ignored files (`node_modules`, the env copies) don't block the remove.
- The worktree's `_wt<n>` databases stay, and the next worktree in that slot
  resets them at setup.
- A worktree folder deleted by hand leaves a stale entry, which
  `git worktree prune` clears.

**The main checkout**:

```bash
git restore --staged --worktree . && git clean -fd   # only once the user chose to discard
git switch <default> && git pull --ff-only origin <default>
git branch -D -- <branch>
```

A refused fast-forward means the local default branch has commits of its own.
Stop and report it, with the branch not yet deleted.

Either way:

- `-D`, since a squash-merged branch never satisfies `-d`.
- The branch on `origin` stays as GitHub left it.

Done when `git branch --list <branch>` prints nothing (or the branch was kept
by choice), and a worktree no longer shows in `git worktree list`.

### 4. Reply

Say what was torn down: the worktree removed or the checkout now on
`<default>`, and the branch deleted or kept, with the merged PR's link. For a
worktree, end with closing its window, which now points at a missing folder.
