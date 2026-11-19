# Take a major update

One item from the report, one PR, on `updates/<package name>`. Proceed is
go-ahead for the bump and the adaptations. Commit and PR wait on their review.

If there's no report in this conversation, do that first (`SKILL.md`) and
continue here.

```
- [ ] 1. Pick the item
- [ ] 2. Confirm the repo and the base
- [ ] 3. Branch
- [ ] 4. Bump
- [ ] 5. Adapt
- [ ] 6. Check
- [ ] 7. Stop for review
- [ ] 8. Commit
- [ ] 9. Open the PR
- [ ] 10. Offer the next
```

### 1. Pick the item

The next item in the report that isn't held back, unless they named one. A group
is one item; the branch uses the lead package name.

### 2. Confirm the repo and the base

- Read the root `package.json`. Use its `packageManager` field for every command
  below.
- `git status` has to be clean. Uncommitted work gets committed (`commit` skill,
  `~/.cursor/skills/commit/SKILL.md`) or stashed first (ask which).
- Fetch, then start from the remote base (`origin/master`, falling back to
  `main` or the remote default).
- If the last take's PR is still open, stop and say so: the lockfile will
  collide. Wait until they merge it, or they say to continue anyway.

### 3. Branch

`git switch -c updates/<package name>` off the base, named as
`~/.cursor/skills/shared/branch-name.md` sets out.

### 4. Bump

Set each of the item's packages to the reported version in every `package.json`
that declares it. Reinstall so the lockfile matches.

### 5. Adapt

The call sites the report named, and any official codemod it named. That's the
work; don't reopen the changelog.

### 6. Check

Run every one of `typecheck`, `lint`, `build` and `test` that exists in the root
`package.json`, from the repo root, in that order. Each is long-running;
background them with a generous timeout.

A check that fails on stale generated artefacts is telling you to run the
repo's `generate` script first, then re-run it.

Green on every check is the bar. Attribute each failure to this bump (or re-run
that check on the base; a failure the base already had is out of scope). Fix
forward. Suppressions don't count as green.

If a failure isn't a small adaptation, stop. Report what broke and leave the
branch uncommitted, with no PR.

### 7. Stop for review

End the turn here, work still uncommitted. Ask them to review, and name the
bump and the adaptations.

### 8. Commit

Once they give the go ahead, follow the `commit` skill. The bump and the
lockfile are one commit, titled
`Bump <package> from v<old major> to v<new major>`
(`Bump dotenv from v17 to v18`), with a group named by its lead package. Each
code adaptation is its own commit on top, named for what it adapts to rather
than for the update.

### 9. Open the PR

Follow the `open-pr` skill (`~/.cursor/skills/open-pr/SKILL.md`), with the bump
commit's title as the PR title. The description carries the version change
(from and to), the breaking changes this major introduces, and for each one why
this app did or didn't need updating. A hop that spans several majors gets a
section per version, each with that version's breaking changes. Link to the
update docs when they exist: a dedicated migration page, or the GitHub repo's
release notes. Include any blocker the report named that you still hit.

### 10. Offer the next

Restate the next item's findings from the report, then offer to take it.
Don't start it. When the list is done, say so.
