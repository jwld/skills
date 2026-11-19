---
name: rebase
description:
  Rebases a branch onto another (defaulting to master), then checks the whole
  branch against the conventions and code patterns the rebase brought in, so
  code written before a pattern landed follows it too. Use whenever rebasing a
  branch, including when the user just says "rebase".
---

# Rebase

Two parts: the rebase itself, then a sweep that makes the whole branch
consistent with the docs and the code it now sits on top of.

## Workflow

```
- [ ] 1. Confirm the branch and base
- [ ] 2. Rebase
- [ ] 3. Find agent doc changes in the new history
- [ ] 4. Find the convention changes the base made in code
- [ ] 5. Check the whole branch against them
- [ ] 6. Say whether fixes are required on top, and pause for review
- [ ] 7. Push once the rebase is complete, and refresh the PR if it changed
```

### 1. Confirm the branch and base

- The base is whatever the user named. Default to `master`, falling back to
  `main` or the remote default when there's no `master`.
- A branch whose open PR targets another branch
  (`gh pr view <branch> --json baseRefName,state`), or whose parent the user
  names, is stacked. Rebase the whole stack, as **Stacked branches** sets out.
- Fetch first and rebase onto the remote ref (`origin/master`), so a stale local
  base isn't used.
- Check `git status`. Uncommitted work has to be committed (`commit` skill) or
  stashed before starting. Ask which rather than deciding.
- Record and quote two things before touching anything:
  - `git rev-parse HEAD` - the way back if the rebase goes wrong.
  - `git merge-base HEAD origin/master` - the old fork point, needed in steps 3
    and 4.

### 2. Rebase

- Run `git rebase origin/master`, or the chosen base.
- Resolve conflicts only where the intent is unambiguous: one side changed,
  import ordering, formatting.
- Stop and ask when both sides changed the same logic. Never guess at a semantic
  conflict, and never `git rebase --skip`.
- If it turns into a mess, `git rebase --abort`, explain why, and ask before
  trying again.
- Hold the push for step 7. The rebased branch goes up together with the
  sweep's fixes.

### 3. Find the agent doc changes the rebase brought in

Agent docs are root and nested `AGENTS.md`, `CONTEXT.md`, and every doc one of
them points at (a `Read X before ...` line), wherever it lives. Any other doc
under `docs/` is for humans and doesn't load, so skip it.

The ones that matter are the conventions the rebase has just moved underneath
the branch, documented after its code was written:

```
git diff <old fork point>..<base> -- '*AGENTS.md' CONTEXT.md '.agents/skills/**' <pointed-at docs>
```

- Match the root `AGENTS.md` with `*AGENTS.md`. A `**/` prefix matches nested
  files only, so the always-loaded file is the one that goes missing.
- Find the pointed-at docs on the base, with
  `git grep -ho '\[`[^`]*\.md`\]' <base> -- '*AGENTS.md'`, so a doc the rebase
  just added is included too.

Read each diff and state the pattern in one line, as what to do rather than what
the doc used to say.

- A change that only reworded an existing rule, or moved it from one `AGENTS.md`
  to another, needs no sweep: say so and move on.
- A rule that newly appears in an `AGENTS.md` after living only in `docs/` is
  new: it never loaded before.
- A workflow skill has nothing to sweep for code.

### 4. Find the convention changes the base made in code

A convention lands as code as often as it lands in a doc, and the doc that does
cover it is often too general to fire on its own ("throw `CodedError`" reads as
satisfied by any throw that isn't a bare `Error`). What the branch has to end up
consistent with is how the base now writes this kind of code, whether the base
introduced a pattern, changed one, or replaced one everywhere. None of that
touches the branch's own files, so nothing conflicts and the branch keeps
whatever it was written with.

```
git log --oneline <old fork point>..<base>
```

- Read the subjects for one that introduces, converts, renames, moves or drops a
  way of writing something, and read its diff. Write down how the base writes it
  now, and how it was written before where the change replaced something.
- Read the base's code around the branch, which is where a convention nobody
  wrote down is visible: the siblings of the files the branch adds to, the
  helper the base now calls, the shape it now gives a file of this kind.
- Confirm each against the base rather than its subject, with
  `git grep -l '<snippet>' <base>` on a line only that way of writing it would
  produce. The new way is everywhere and the old one has no hits left.
- Check the branch's side too, since a subject can bury the change: the imports,
  helpers and throw sites its added lines use should all still exist in the
  base. One with no hits is either defined by the branch or retired underneath
  it.

### 5. Check the whole branch against them

- The unit is the net branch diff (`git diff <base>...HEAD`), all of it. Every
  commit on the branch predates these changes, so there's no part of it that can
  be assumed to follow them.
- Check code the branch adds and edits against those conventions. Files the
  branch doesn't touch are out of scope; mention them if there's a lot, but
  don't fix them here.
- Patterns often show up as omissions rather than wrong lines, so check for what
  should be there and isn't: a missing barrel import, a test in the wrong
  folder, a type declared at the wrong scope, a field named after the wrong
  sense of a term.
- Write every site the branch adds or edits the way the base writes it now, and
  take the rest of the change with it rather than the one line that differs
  (what the site now has to pass or call, and whatever reads its result).

### 6. Say whether fixes are required on top

Lead with one verdict, before any finding, pattern or "no sweep" note:

- **Fixes required on top.** The branch doesn't follow what the rebase brought
  in. One line per fix, the file first. Apply them, verify as
  [shared/verify.md](../shared/verify.md) sets out, then leave them uncommitted in
  the tree so the user reviews them in the review tool, and stop with the
  branch unpushed. Don't offer to commit them. Only once the user asks for a
  commit, make one on top of the rebase, then go to step 7. Folding them into
  the commits they belong to is only when the user asks.
- **Nothing to fix.** The branch is already consistent. Say that and go to
  step 7.

### 7. Push once the rebase is complete, and refresh the PR if it changed

The rebase is complete when the sweep has nothing left to fix: straight after a
**Nothing to fix** verdict, or once the user's fixes are committed. Push then,
and only when the branch has already been pushed. Follow the `push` skill's
**Which push** section ([push/SKILL.md](../push/SKILL.md)). The rebase rewrote
the history, so it takes force with lease.

After the push, refresh the branch's open PR when the rebase changed what the
branch does: a conflict resolved by rewriting the branch's code, sweep fixes
committed on top, or a commit dropped because the base already had it. Follow
step 4 of the `update-pr` skill ([update-pr/SKILL.md](../update-pr/SKILL.md)). A
clean rebase that left the net diff as it was needs no refresh.

## Stacked branches

Parent first, so every branch lands on a parent that's already current.

- Walk up from the current branch, following each open PR's base, until you
  reach the branch whose base is `master`. That branch is the top of the tree.
- Before touching any of them, record every branch's tip and its old fork point
  with its parent (`git merge-base <branch> <parent>`).
- Run the whole workflow on the top branch against `origin/master`, then on each
  branch below it against its freshly rebased local parent. Finish each one,
  fixes included, before starting the next, so the child inherits the parent's
  fixes.
- Rebase each child with `git rebase --onto <parent> <old fork point>`, so the
  parent's pre-rebase commits aren't replayed onto it.
- Finish on the branch the user started on.
