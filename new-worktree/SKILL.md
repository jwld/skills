---
name: new-worktree
description: >-
  Opens a fresh git worktree in a new Cursor window, so each window's chats
  and review bar cover only its own work; also lists worktrees. Use when the
  user wants a new worktree or window, to work on several things at once, or
  to see their worktrees.
---

# New worktree

One Cursor window per worktree. A worktree is a second checkout of the same
repo, sharing one `.git`. The review bar collects the edits made by the chats
in its own window, so a worktree's work happens in a chat in its window, with
git run from inside it.

Work out which the user wants: **Start** (the default) or **List**.

## Start

Start needs nothing from the user. The work is decided in the new window.

```
- [ ] 1. Add the worktree
- [ ] 2. Set it up
- [ ] 3. Open the window
```

### 1. Add the worktree

The main checkout is the first path `git worktree list` prints. Worktrees sit
beside it as numbered slots, `<parent>/<repo>.worktrees/<repo>-<n>` with the
lowest free `<n>`: the PropertyWerks repo at `~/GitHub/pwerks/propertywerks`
gets `~/GitHub/pwerks/propertywerks.worktrees/propertywerks-1`.

`git fetch origin`, then from the main checkout:

```bash
# Fresh work: no branch yet. The commit skill names one at the first commit.
git worktree add --detach <path> origin/<default>

# An existing branch the user named. One only on origin gets a local branch tracking it.
git worktree add <path> <branch>
```

`<default>` is the default branch, as
[shared/base-branch.md](../shared/base-branch.md) sets out. When git refuses a
branch as "already used by worktree", say which worktree holds it: its window
may already be open.

Done when `git worktree list` shows `<path>`.

### 2. Set it up

A worktree starts with tracked files only: no `node_modules`, no `.env` files,
no gitignored generated code.

- **The repo has `.cursor/worktrees.json`** - Cursor runs it only for
  worktrees it creates itself, so run it here. Take `setup-worktree-unix`,
  else `setup-worktree`: an array of commands, or a script path relative to
  the JSON file. Run them in the worktree with `ROOT_WORKTREE_PATH` set to the
  main checkout, in place of the copy, install and codegen below.
- **Otherwise** - copy the ignored env files across from the main checkout,
  then install dependencies with the package manager whose lockfile sits at
  the root, frozen to that lockfile (`pnpm install --frozen-lockfile`,
  `npm ci`), plus any fresh-clone setup the repo's `AGENTS.md` names. Then
  run the repo's codegen (a root `generate` script, or whatever its
  `AGENTS.md` files say to run after switching branch). Until it runs, every
  import of the generated code fails to resolve, so a lint autofix rewrites
  files it shouldn't touch (an import-order rule moves them all) and a
  typecheck fails for no real reason.

```bash
cd <main checkout>
git ls-files -oi --exclude-standard -- '*.env*' ':!:*node_modules*' | while read -r f; do
  mkdir -p "<path>/$(dirname "$f")" && cp "$f" "<path>/$f"
done
```

Either way, give the worktree databases of its own, so a migration or test run
in one worktree never resets another's. Suffix the database name in every
local database URL in the worktree's env files with `_wt<n>`, its slot number,
leaving every remote URL as it is:

```bash
cd <path>
git ls-files -oi --exclude-standard -- '*.env*' ':!:*node_modules*' | xargs sed -E -i '' \
  's#(postgres(ql)?://([^/@]*@)?(localhost|127\.0\.0\.1)(:[0-9]+)?/)([A-Za-z0-9_]+)#\1\6_wt<n>#g'
```

- Keep the `wt` in the suffix. A bare `_<n>` can land on a name the repo's
  tooling derives from the main checkout's database (a test runner cloning it
  once per worker as `<name>_<worker>`).
- The suffixed databases are new, or still hold an earlier worktree's schema
  from the same slot. Name the repo's command that resets the local databases
  (a reset, not a migrate, which trips over that leftover schema) in the
  reply, for the user to run in the new window where the repo's `AGENTS.md`
  keeps database writes to them. Run it here otherwise.

When a setup command fails, stop and show its error. The worktree stays, for
the user to decide on.

Done when every setup command exits 0.

### 3. Open the window

`cursor -n --classic <path>` opens it in a new editor window; `--classic`
keeps it out of the Agents Window.

Reply with the path, what it's checked out on, what setup ran, the database
command left for the user if there is one, and that the window is open.

## List

`git worktree list`, then one line for each worktree other than the main
checkout: its path, its branch (or detached), whether it has uncommitted
changes (`git -C <path> status --short`), and a branch's open PR URL if it
has one (`gh pr list --head <branch> --json url --jq '.[0].url'`).

## Tear down

The user runs `/teardown` in a worktree's window once its PR has merged
([teardown/SKILL.md](../teardown/SKILL.md)).
