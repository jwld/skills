---
name: packages-minor
description: >-
  Runs the repo's `updates:minor` script on a `minor-package-updates` branch,
  gets the checks green, then commits and opens a ready-for-merge PR. Use when
  the user asks to update minor packages, take minor dependency updates, or run
  `updates:minor`.
---

# Update minor packages

Take every minor and patch bump the repo's `updates:minor` script offers, prove
the local checks still pass, and ship it. Leave the test suite to CI.

Invoking this skill is the go-ahead for the commit and the push, so don't ask
again for either.

## Workflow

```
- [ ] 1. Confirm the repo and the base
- [ ] 2. Branch
- [ ] 3. Run the update
- [ ] 4. Check
- [ ] 5. Fix or pin what broke
- [ ] 6. Commit
- [ ] 7. Open the PR
```

### 1. Confirm the repo and the base

- Read the root `package.json`. It has to have an `updates:minor` script. Stop
  and say so if it doesn't. Use its `packageManager` field for every command
  below.
- `git status` has to be clean. Uncommitted work gets committed (`commit` skill,
  [commit/SKILL.md](../commit/SKILL.md)) or stashed first (ask which).
- Fetch, then start from the remote base (`origin/master`, falling back to
  `main` or the remote default), so the bump doesn't sit on a stale tree.

### 2. Branch

`git switch -c minor-package-updates` off the base, named as
[shared/branch-name.md](../shared/branch-name.md) sets out.

### 3. Run the update

Run the script (`yarn updates:minor`). It rewrites every workspace
`package.json`, deletes the lockfile and reinstalls, so it takes minutes: give
it a long timeout rather than polling it to death.

Then read `git diff -- '**/package.json' package.json` and keep the list of
bumped packages. It's what you attribute failures against in step 5.

If the script bumps nothing, say so and stop. There's no branch to keep.

### 4. Check

Run every one of `typecheck`, `lint` and `build` that exists in the root
`package.json`, from the repo root, in that order (cheapest signal first). Each
is long-running; background them with a generous timeout.

Leave `test` to GitHub CI. A full local suite is too slow, and CI catches stray
failures once the PR is up.

A check that fails on stale generated artefacts rather than the bump is telling
you to run the repo's `generate` script first, then re-run it.

### 5. Fix or pin what broke

Green on every check is the bar. For each failure:

- **Attribute it.** Tie the error to one bumped package (the renamed export, the
  tightened type, the lint rule that now fires). When you can't tell whether the
  bump caused it, re-run that one check on the base and compare; a failure the
  base already had is out of scope, so leave it and note it.
- **Fix forward** when the adaptation is small and the intent is obvious: a
  renamed import, a changed signature, a new required option, code that
  satisfies the new rule.
- **Pin back** when the fix is large, risky, or the intended replacement isn't
  clear. Restore that one package to its previous version in the `package.json`
  that declares it, reinstall to update the lockfile, and record which package
  and why for the PR.
- Suppressions don't count as green: adapt the code, or pin the package back.
- Re-run the full check set after any fix or pin (a fix in one workspace can
  break another).

If a failure is neither fixable nor pinnable, stop there. Report what broke and
leave the branch as it is, uncommitted, with no PR.

### 6. Commit

Follow the `commit` skill. The bumps and the lockfile are one commit, pins
included (choosing versions is one idea). Each code adaptation is its own commit
on top, named for what it adapts to rather than for the update.

Don't list bumped packages as bullets; the diff has them.

### 7. Open the PR

Follow the `open-pr` skill ([open-pr/SKILL.md](../open-pr/SKILL.md)) from its
step 3. Its step 1 is already answered: **does not need review**, so the
write-up audience is historical reference and the PR gets the
`[PR] ready for merge` label.

The description carries what the diff can't: any package pinned back and why,
and any code that had to adapt.
