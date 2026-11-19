---
name: backup-work
description: >-
  Puts local work aside on a `backup/` branch to come back to later: commits it
  there, keeps it local, and switches back to the default branch. Use when the
  user asks to back up, park or shelve work for later, on the checked-out
  branch or another one.
---

# Backup work

Put the work aside on a local `backup/` branch and leave the checkout clear.

The user asking for a backup is the go-ahead to commit it. Never push: a
backup stays local.

## Workflow

```
- [ ] 1. Pick the branch
- [ ] 2. Name the backup branch
- [ ] 3. Commit (checkout only)
- [ ] 4. Switch to the default branch (checkout only)
- [ ] 5. Reply
```

### 1. Pick the branch

The branch to back up is the one the user names, or the checked-out branch
(`git branch --show-current`) when they name none.

Find the default branch as [shared/base-branch.md](../shared/base-branch.md)
sets out. Stop and ask when the user names the default branch.

A named branch that isn't checked out holds only its commits, since
uncommitted work belongs to the checkout. It skips steps 3 and 4: rename it,
then reply.

### 2. Name the backup branch

By the branch to back up:

- **Already `backup/...`** - keep it as it is.
- **The default branch, or a detached HEAD** (as the checkout) - make a new
  branch, `git switch -c backup/<name>`, named after the work in kebab-case.
- **Any other branch** - rename it, `git branch -m <branch> backup/<name>`,
  where `<name>` is the branch's name with any leading `jack/` dropped
  (`jack/menu-nodes` becomes `backup/menu-nodes`). The work is only ever
  the user's own, so the `jack/` prefix has nothing left to say.

After a rename, run `git branch --unset-upstream backup/<name>` when the
branch had one. The remote branch, and any PR on it, stays as it was.

If `backup/<name>` already exists, stop and ask what to do with it.

Done when the work is on a `backup/` branch.

### 3. Commit (checkout only)

Commit everything uncommitted, untracked files included (`git add -A`). A
backup is a snapshot, so scrap and half-done edits go in too, all in one
commit.

Write the message as the `commit` skill's **Message shape** and **Title** set
out ([commit/SKILL.md](../commit/SKILL.md)). Its one-commit-per-idea rule doesn't
apply here.

Work in progress needn't pass checks: if a hook rejects the commit, commit
again with `--no-verify`.

A clean tree has nothing to commit.

Done when `git status` shows a clean tree.

### 4. Switch to the default branch (checkout only)

`git switch <default>`.

### 5. Reply

Say which `backup/` branch holds the work, what was committed (or that there
was nothing to commit), and that it's local only. After a rename from a
branch with an upstream, say the remote copy was left alone.
