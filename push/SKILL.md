---
name: push
description: >-
  Commits any uncommitted work with the commit skill, then pushes the branch to
  origin. Use when the user runs /push, or says to push.
---

# Push

Get the branch's work onto the remote: commit what's uncommitted, then push.

Invoking this skill is the go-ahead for both, so don't ask again for either.

## Which push

Which command to run, here and in every other skill that pushes. The mode
follows what happened to the branch, never preference:

- **Plain** - `git push origin`, adding `-u` when the branch has no upstream.
  The default, for a branch that only gained commits.
- **Force with lease** - `git push --force-with-lease`, for a branch whose
  history was rewritten under commits already on the remote: a rebase, an
  amend, a squash. Never plain `--force`.

A rejected plain push means the remote has moved on. Stop and report it, since
a rebase or a merge is the user's call.

A rejected force-with-lease means someone else has pushed to the branch. Stop
and report that too. Never escalate to `--force`.

### 1. Check the branch

Stop on a protected `master`, as
[shared/branch-name.md](../shared/branch-name.md) sets out: the work needs its
own branch and a PR (`open-pr` skill, [open-pr/SKILL.md](../open-pr/SKILL.md)).
Other remotes (`jwld/skills`, completionism) can push to `master`.

### 2. Commit

Deal with uncommitted work as [shared/uncommitted.md](../shared/uncommitted.md)'s
**Commit it** sets out.

### 3. Push

Push as **Which push** decides.

Say what was committed and pushed, and where nothing needed either.
