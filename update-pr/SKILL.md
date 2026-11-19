---
name: update-pr
description: >-
  Pushes unpushed local commits, syncs the linked issue, and refreshes the
  open pull request's write-up when it no longer matches. Use when the user says
  "update the PR", "update this PR", or has unpushed commits on a branch that
  already has a pull request.
---

# Update a PR

Push the current branch (`push` skill, [push/SKILL.md](../push/SKILL.md)), sync
the issue with `update-issue` ([update-issue/SKILL.md](../update-issue/SKILL.md)), then
refresh the existing pull request's title and body from `write-up-branch`
([write-up-branch/SKILL.md](../write-up-branch/SKILL.md)) when they no longer match. The
issue goes first, since the write-up's outstanding-work notice reads it.

## Workflow

```
- [ ] 1. Confirm the branch and PR - commit intentional uncommitted work
- [ ] 2. Push
- [ ] 3. Sync the issue
- [ ] 4. Refresh the write-up if needed
- [ ] 5. Reply with the URL
```

### 1. Confirm the branch and PR

- Current branch must not be the default branch
  ([shared/base-branch.md](../shared/base-branch.md)). Stop if it is.
- There must already be a pull request for the branch (`gh pr view`). If there
  isn't, stop and point at the `open-pr` skill. Do not open one.
- Check `git status`. Commit uncommitted work that looks intentional with the
  `commit` skill ([commit/SKILL.md](../commit/SKILL.md)). Leave leftover scrap (a
  stray console log) uncommitted and say so.
- Note what's unpushed (`git log @{u}..HEAD`). A description-only refresh is
  still fine when there's nothing to push.

### 2. Push

- Push as the `push` skill's **Which push** section decides
  ([push/SKILL.md](../push/SKILL.md)). A branch rebased, amended or squashed
  since its last push takes force with lease.
- Skip this step when there's nothing unpushed.

### 3. Sync the issue

Follow the `update-issue` skill ([update-issue/SKILL.md](../update-issue/SKILL.md)). When
the PR has no issue, follow the `open-pr` skill's **Find or open the issue**
step ([open-pr/SKILL.md](../open-pr/SKILL.md)). The refreshed write-up gains a
`Resolves` line only when that step opens one.

### 4. Refresh the write-up if needed

- Read the current title, body, and labels with `gh pr view`.
- In propertywerks, take the audience from the `[PR]` label the PR carries, as
  step 1 of `open-pr` ([open-pr/SKILL.md](../open-pr/SKILL.md)) maps them. Don't
  ask; ask only when the PR carries none of them.
- The exception is a PR labelled `[PR] needs review` and assigned to the user
  (`gh pr view --json assignees`, against `gh api user --jq .login`). Ask with
  `AskQuestion` whether it's now ready for merge. On a yes, swap the label for
  `[PR] ready for merge` (`gh pr edit --remove-label "[PR] needs review"
  --add-label "[PR] ready for merge"`) and write it up for historical
  reference. On a no, keep the label and the human-review write-up.
- Anywhere else, write-up audience is historical reference.
- Follow `write-up-branch` from its step 2 onwards, skipping its
  uncommitted-work check. Use its title and description. Do not emit the three
  write-up code blocks, and do not use the squash output.
- Hold the title to the net diff on its own, not only the body. A title
  written when the PR opened goes stale once the branch widens (a second fix,
  a migration that grew a cleanup): retitle it to name the whole of the work,
  still one clause as `write-up-branch`'s **Output 1** sets out.
- Hold the whole body to `write-up-branch`'s size rules and to every section
  the audience calls for, not only the lines the newest commits touch. A body
  missing a section that audience requires (a human-review write-up with no
  `## Suggested review order`) gains it, however long it went without. A branch
  that grew since the last write-up may need its sections regrouped and its
  squash message cut back, rather than a bullet added for each new change.
- Leave the existing title and body when they still match the net end state
  and the issue's outstanding items.
- When they don't, `gh pr edit --title "..." --body-file -`, passing the body
  as [shared/gh-body.md](../shared/gh-body.md) sets out.

### 5. Reply with the URL

Give the PR URL. Say whether the issue changed (or give its URL when step 3
opened it), and whether the write-up did. No recap of either write-up.
