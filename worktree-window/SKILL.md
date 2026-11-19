---
name: worktree-window
description: >-
  Starts a piece of work in its own git worktree, opened in a new Cursor
  window, so each window's chats and review bar cover only that work; also
  lists and removes worktrees. Use when the user wants to work on several
  things at once, start something in a worktree or new window, or is done
  with a worktree.
---

# Worktree window

One piece of work per worktree, and one Cursor window per worktree. A worktree
is a second checkout of the same repo on its own branch, sharing one `.git`.

## One window per worktree

The review bar ("Keep all changes", "Review next file") collects the edits
made by the chats in its own window. So a worktree's edits come from a chat in
that worktree's window:

- From a chat whose workspace is another checkout, leave the worktree's files
  to its own window, the setup in **Start** aside. Hand the work over as a
  first message for the user to paste into a chat there.
- Run git for a worktree from inside it. `commit`, `push` and the other git
  skills act on the checkout the shell sits in, which in each window is that
  window's own.
- A branch is checked out in one worktree at a time. When git refuses a
  branch as "already used by worktree", say which worktree holds it (`git
  worktree list`) rather than forcing it.

Work out which the user wants: **Start**, **List** or **Remove**.

## Start

```
- [ ] 1. Find the main checkout
- [ ] 2. Pick the branch
- [ ] 3. Add the worktree
- [ ] 4. Set it up
- [ ] 5. Open the window
- [ ] 6. Reply
```

### 1. Find the main checkout

The main checkout is the first path `git worktree list` prints, whichever
worktree it runs from. Worktrees sit beside it, in
`<parent>/<repo>.worktrees/<name>`: the PropertyWerks repo at
`~/GitHub/pwerks/propertywerks` keeps them in
`~/GitHub/pwerks/propertywerks.worktrees/`.

### 2. Pick the branch

- **New work** - a new branch, named as
  [shared/branch-name.md](../shared/branch-name.md) sets out, off the default
  branch as [shared/base-branch.md](../shared/base-branch.md) sets out.
- **An existing branch** the user names - local, or only on `origin`. When
  `git worktree list` shows it already checked out, stop and say where: its
  window may already be open.

`<name>` is the branch name with any leading `jack/` dropped. If
`<repo>.worktrees/<name>` already exists, stop and ask what to do with it.

### 3. Add the worktree

`git fetch origin`, then from the main checkout:

```bash
# New work. --no-track keeps origin/<default> from becoming its upstream.
git worktree add --no-track -b <branch> <path> origin/<default>

# An existing branch. One only on origin gets a local branch tracking it.
git worktree add <path> <branch>
```

Done when `git worktree list` shows `<path>` on `<branch>`.

### 4. Set it up

A worktree starts with tracked files only: no `node_modules`, no `.env` files.

- **The repo has `.cursor/worktrees.json`** - Cursor runs it only for
  worktrees it creates itself, so run it here. Take `setup-worktree-unix`,
  else `setup-worktree`: an array of commands, or a script path relative to
  the JSON file. Run them in the worktree with `ROOT_WORKTREE_PATH` set to the
  main checkout. That is the whole step.
- **Otherwise** - copy the ignored env files across from the main checkout,
  then install dependencies with the package manager whose lockfile sits at
  the root, frozen to that lockfile (`pnpm install --frozen-lockfile`,
  `npm ci`), plus any fresh-clone setup the repo's `AGENTS.md` names.

```bash
cd <main checkout>
git ls-files -oi --exclude-standard -- '*.env*' ':!:*node_modules*' | while read -r f; do
  mkdir -p "<path>/$(dirname "$f")" && cp "$f" "<path>/$f"
done
```

When a setup command fails, stop and show its error. The worktree stays, for
the user to decide on.

Done when every setup command exits 0.

### 5. Open the window

`cursor -n --classic <path>` opens it in a new editor window; `--classic`
keeps it out of the Agents Window.

### 6. Reply

Give the path, the branch, what setup ran, and that the window is open.

When the user described the work, end with a first message for a new chat in
that window, in a code block: the task as they put it, plus anything from this
chat the new one needs, since it starts with none of this conversation.

## List

`git worktree list`, then one line for each worktree other than the main
checkout: its path, its branch, whether it has uncommitted changes
(`git -C <path> status --short`), and its open PR's URL if it has one
(`gh pr list --head <branch> --json url --jq '.[0].url'`).

## Remove

Run from the main checkout, for the worktree the user is done with:

1. `git worktree remove <path>`. Ignored files (`node_modules`, the env
   copies) don't block it.
2. When git refuses over modified or untracked files, show
   `git -C <path> status --short` and put the choice as
   [shared/choices.md](../shared/choices.md) sets out: commit them with the
   `commit` skill ([commit/SKILL.md](../commit/SKILL.md)) and remove, or
   discard them with `git worktree remove --force <path>`.
3. A worktree folder deleted by hand leaves a stale entry, which
   `git worktree prune` clears.

Removing a worktree keeps its branch and commits. Reply with what was removed,
that the branch is still there (the `merged-branches` skill clears it once its
PR merges, [merged-branches/SKILL.md](../merged-branches/SKILL.md)), and to
close that worktree's window, which now points at a missing folder.
