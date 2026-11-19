---
name: update-issue
description: >-
  Refreshes a GitHub issue's write-up so its plan and outstanding items match
  the work that has landed. Use when the user says "update the issue", or
  when another skill needs the issue kept in sync.
---

# Update an issue

Keep the issue's write-up true of the work that has landed: the plan, the
outstanding items, and any summary that diverged. Leave the working tree and
the PR alone.

## Write-up

The issue says why the work exists, what has to land before its PR merges, and
how far the plan has got. What the diff does lives in the PR write-up. A long
PR buries what's posted on it, so follow-ups go on the issue.

- Title: imperative, sentence case, no prefix. Title it as you would the PR.
  When one issue spans several PRs, the title is the whole piece of work, not
  this PR's title.
- Body, in order:
  - `## Problem` - why the work exists: the problem and the evidence behind
    it, as the issue was opened with. Nothing regenerates it, so carry it over
    word for word. Where an issue has none (a bare checklist, or a body from
    before this layout), put its opening prose under the heading, or write a
    sentence or two from what the session established about why, and ask when
    nothing does.
  - `## Outstanding` - only while it has items (see **Outstanding** below).
  - `## Flow` - only where the issue has a flow chart: the mermaid chart on
    its own, kept through every rewrite and redrawn to match the plan rather
    than dropped or folded into `## Plan`.
  - `## Plan` - what the work delivers, and how it ships (see **Plan**
    below).

A `## Suggested fix` the issue was opened with stays until the issue has a
plan, then goes: the plan is the fix that was taken.

### Plan

A one-sentence lead on what the work changes, then a bullet per change that
addresses the problem: what it does, naming the code it touches, and which
part of the problem it fixes. Cover every part of `## Problem`, and the
structural changes that ship with it. Bullets, never a paragraph. New
patterns, docs and review notes stay in the PR.

**One PR.** The lead and its bullets. The PR shows how far it has got.

```
## Plan

Moves the card-adding logic into the `createPaymentCard` resolver as TypeScript, closing the ownership hole and both null cases on the way.

- Refuse a member-app request whose `memberId` isn't the signed-in member's with `ErrCode.Unauthorized`, in a `checkMemberAccess` guard that runs before any read
- Reject a member with no name with `ErrCode.InvalidData` before a Stripe customer is created
- Move `addPaymentCard` out of `@/helpers` into the resolver as TS, with a TS spec
```

**Several PRs.** The lead, then a checkbox per PR, numbered from 1 in the
order they merge: the PR's title, then its bullets nested under it. Add the
PR's number after the title once it's open, so GitHub shows its state.

```
## Plan

Moves every app's routes and links onto one shared path table.

- [x] PR1: Add the shared path primitives (#412)
  - Add the path table in `@acme/routes`, one entry per screen
  - Add `usePath` and `useNavigate` hooks that read it
- [ ] PR2: Migrate member-app (#418)
  - Move its route definitions and links onto the table
- [ ] PR3: Migrate admin-app
  - The same move for admin-app
```

Tick a PR once it has merged. The last one closes the issue as it merges, so
its box can stay open.

**Phases.** Work run through the `phased-implementation` skill groups the
plan's bullets by phase instead of listing them flat: the lead, then a
numbered list in commit order, each entry the phase's name in bold followed by
two or three short bullets on what it changes. No checkboxes, since the phases
are for a reader who isn't on the PR to follow the shape of the work, and no
file-by-file detail, which stays in the PR write-up. Never a separate phases
list repeating the bullets. On several PRs, the phases nest under their PR's
checkbox.

### Outstanding

Work that has to land before a PR merges but isn't in its diff yet, raised
after the plan (in a meeting, say). Each group of items links the comment on
this issue it came from, then gives one unticked checkbox per item, a line
each:

```
## Outstanding

From [the meeting on Tue 13 Oct](<comment URL>):

- [ ] Say we'll reply by email in the escalation message
```

On an issue spanning several PRs, start each item with the PR it blocks
(`- [ ] PR2: ...`), numbered as the plan numbers it.

Remove an item once the branch does it, rather than ticking it: the commits
and the comment keep its history, and anything left in the section is still
owed. Keep the rest word for word. Drop a group's link with its last item, and
the heading with the last item of all. Work that can wait until after the PR
it touches merges is a new issue, not an item here.

## Workflow

```
- [ ] 1. Find the issue
- [ ] 2. Check it against the work
- [ ] 3. Edit when it isn't in sync
```

### 1. Find the issue

Look in this order, and stop at the first hit:

- `Resolves #N`, `Fixes #N`, `Closes #N` or `Part of #N` on the open PR
- `gh pr view --json closingIssues`
- The issue opened earlier in this session

Ask when none of those turn one up. Skip this skill when there is no issue;
do not open one here. A PR without one gets it from the `open-pr` skill's
**Find or open the issue** step (`~/.cursor/skills/open-pr/SKILL.md`).

### 2. Check it against the work

Read the issue with `gh issue view`. Hold it to **Write-up** above, or to its
own format where someone else wrote it (see step 3).

- `## Problem` comes first, carried over word for word.
- Every `## Outstanding` item the branch now does is removed.
- The lead and its bullets still name the work as it stands, not the plan it
  started as, and the bullets cover every part of the problem.
- Several PRs have a checkbox each, and one PR has none. A checklist of
  steps from an older layout goes; the numbered phases grouping the bullets
  stay.
- Each open PR has its number, and each merged one is ticked.
- A PR that turned out differently has its bullets corrected, and work that
  split off into a PR of its own gets a checkbox.
- Do not pull PR-only detail into the issue (new patterns, docs, the squash
  message, review notes).

### 3. Edit when it isn't in sync

- Keep an issue someone else wrote (`gh issue view --json author`, against
  `gh api user --jq .login`) in its author's format rather than **Write-up**
  above. Correct what the work has made untrue, and extend a section that
  tracks the work (a commit list) to cover it, under their headings and in
  their voice. Keep everything still true word for word, and leave its
  assignees alone.
- Leave the existing title and body when they already match.
- When they don't, `gh issue edit --title "..." --body-file -`, passing the
  body as `~/.cursor/skills/shared/gh-body.md` sets out. Rewrite the whole
  body, so a stale sentence cannot linger under a patched heading, but keep the
  problem and the outstanding items still owed word for word.
- Assign it as the `open-issue` skill's **Assignee** section decides
  (`~/.cursor/skills/open-issue/SKILL.md`), with
  `gh issue edit --add-assignee @me`.
- Say whether the issue changed. No recap of the write-up.
