---
name: open-pr
description: >-
  Opens a GitHub pull request from the current branch, using a write-up-branch
  title and description. Use when the user says "open a PR", "create a PR", or
  "open this as a PR".
---

# Open a PR

Create a GitHub pull request for the current branch. Title and body come from
the `write-up-branch` skill ([write-up-branch/SKILL.md](../write-up-branch/SKILL.md)).

A PR gets an issue when [shared/issue.md](../shared/issue.md) says so. The issue
holds why the work exists and what's still owed before merge; the PR holds what
the diff does.

## Workflow

```
- [ ] 1. Kind of PR (propertywerks only, ready for merge unless they said)
- [ ] 2. Commit any uncommitted work
- [ ] 3. Find or open the issue
- [ ] 4. Write up the branch
- [ ] 5. Push and open the PR
- [ ] 6. Reply with the URLs
```

### 1. Kind of PR

Only in the `propertywerks` GitHub org. Check the remote owner first. Anywhere
else, skip this step: write-up audience is historical reference, and step 5
creates the PR with title and body only.

In propertywerks, the PR is ready for merge unless the user said otherwise when
they called this skill. Do not ask. The repo's `docs/conventions/github.md`
sets each kind's label, reviewer and assignee.

- **Ready for merge** - the default. Write-up audience is historical reference.
- **Needs review** - only when they said so. Write-up audience is human review.
- **In progress** - only when they said so. Write-up audience is historical
  reference.

The kind answers step 1 of `write-up-branch`. Do not ask that one. Ready for
merge and in progress are historical reference.

The mapping reads in reverse too: a PR already carrying one of those labels
takes its audience from the label, so a later pass over it reads the label
rather than asking again. `[PR] in review`, `[PR] changes requested`,
`[PR] needs re-review`, `[PR] needs final pass` and `[PR] in final pass` are
later stages of review, so they map to human review as well.

### 2. Commit any uncommitted work

Deal with uncommitted work as [shared/uncommitted.md](../shared/uncommitted.md)'s
**Commit it** sets out.

### 3. Find or open the issue

Look for the branch's issue as the `update-issue` skill's **Find the issue**
step sets out ([update-issue/SKILL.md](../update-issue/SKILL.md)), without asking when
none turns up. Whether to open one is [shared/issue.md](../shared/issue.md).

- **Found** - follow `update-issue` so the issue matches the work now on the
  branch.
- **None, and it needs one** - open one with the `open-issue` skill
  ([open-issue/SKILL.md](../open-issue/SKILL.md)), with the body it gives an issue
  opened for a PR. It's work under way, so assign `@me`. Don't ask first; this
  skill is the go-ahead.
- **None, and it doesn't** - skip. The PR has no `Resolves` line.

### 4. Write up the branch

Follow `write-up-branch` from its step 2 onwards, skipping its uncommitted-work
check (already handled above). Use its title and description as the PR title and
body, with `Resolves #<issue>` (or `Part of #<issue>`) for the issue from step 3
when there is one. Do not emit the write-up code blocks. The squash message is
the description's `## Squash message` section, so it needs no output of its own.

### 5. Push and open the PR

- Push as the `push` skill's **Which push** section decides
  ([push/SKILL.md](../push/SKILL.md)). A branch being opened as a PR has only
  gained commits, so it takes a plain push.
- Base is the default branch, as
  [shared/base-branch.md](../shared/base-branch.md) sets out.
- If a PR already exists for the branch, stop and say so. Do not open a second
  one, and do not overwrite the existing title or body.
- Create with `gh pr create`, passing `--title` and the body as
  [shared/gh-body.md](../shared/gh-body.md) sets out. In propertywerks, apply the
  label, reviewer and assignee for the kind from step 1 in the same step. Each
  command below takes the body's heredoc after it.

```
gh pr create --base <default> --title "..." --body-file - <<'EOF'
gh pr create --base <default> --title "..." --label "<label>" [--reviewer <login>] [--assignee <login>] --body-file - <<'EOF'
```

### 6. Reply with the URLs

Give the PR URL, and the issue URL when step 3 opened one. No recap of the
write-up, and no squash message: it is already in the description.
