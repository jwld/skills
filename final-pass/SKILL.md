---
name: final-pass
description: >-
  Takes a final pass over a finished branch, or the component of it the user
  names, section by section, fixing each agreed finding locally instead of
  leaving review comments. Use only when the user runs /final-pass.
disable-model-invocation: true
---

# Final pass

The user's own pass over a finished branch, often another author's work, before
it merges. It looks for what `review-pr` looks for, but each finding the user
agrees to is fixed and committed in the working tree rather than posted. The
pass posts no comment, review or reply. GitHub changes only on the user's yes:
to retargeting a chained or stacked PR at the default branch, to handing the PR back after
the deep dive, to the `update-pr` offer after each section, and to the closing
label. A finding only the author can settle is held for `review-pr` to post.
Every chat message the pass writes is succinct, as
[shared/succinct.md](../shared/succinct.md) sets out.

## Scope

When the user names a component or piece of code (`/final-pass TaskForm`), the
pass covers only the branch's changes to it: the paths it lives in. Otherwise
it covers the whole branch. Where the named code has no changes on the branch,
say so and ask whether to pass over it as it stands.

## Workflow

```
- [ ] 1. Open the branch
- [ ] 2. Check whether it can land on master
- [ ] 3. Deep dive for fundamental problems
- [ ] 4. Section the branch, then summarise it
- [ ] 5. Review the section
- [ ] 6. Work its findings
- [ ] 7. Look through the section
- [ ] 8. Document the section's fixes
- [ ] 9. Next section, back to 5
- [ ] 10. Report
```

### 1. Open the branch

- Read the PR, where there is one, for what the branch sets out to do:

  ```bash
  gh pr view --json \
    number,title,body,url,author,headRefName,baseRefName,labels,state
  ```

  Stop if it's merged or closed.
- Check out the branch if you aren't on it, and pull, so the fixes land on the
  author's latest work.
- Work from the net diff vs the parent, gathered as
  [shared/net-diff.md](../shared/net-diff.md) sets out, narrowed to the scope.

### 2. Check whether it can land on master

Where the PR's base isn't the default branch
([shared/base-branch.md](../shared/base-branch.md)), it's chained onto a PR
bound for the default branch, or stacked into a feature branch. Landing it on
the default branch by itself is always preferable, so assess
whether it can, from the whole PR whatever the scope:

- **Leans on the base** - gather what the base adds over the default branch
  (`git log` and `git diff origin/<default>...origin/<base>`), and name each
  thing the PR uses from it: a helper, a type, a migration, a renamed symbol, a
  file only the base adds.
- **Ships alone** - name anything that would reach users half-built on the
  default branch without its base.

Give the verdict in a line or two, with the reason:

- **Stands alone** - recommend retargeting, and wait. On a yes, run the
  `rebase` skill ([rebase/SKILL.md](../rebase/SKILL.md)) onto the default
  branch, with `git rebase --onto origin/<default> <fork point with base>` so
  the base's commits stay behind, rather than its **Stacked branches**. Once
  it pushes, retarget with `gh pr edit --base <default>`, then gather the net
  diff afresh against the new base. Where the PR sits in a GitHub stack
  (`gh api repos/<owner>/<repo>/pulls/<number> --jq .stack` isn't null),
  GitHub refuses that base change, so retarget it with the `reorganise-stack`
  skill ([reorganise-stack/SKILL.md](../reorganise-stack/SKILL.md)) instead.
- **Part stands alone** - name the part. The report offers it to `land-first`.
- **Needs its base** - carry on.

### 3. Deep dive for fundamental problems

Before sectioning, run the `deep-dive` skill
([deep-dive/SKILL.md](../deep-dive/SKILL.md)) over the scope. A problem is
fundamental when the rest of the pass would be reviewing on top of it: the
wrong approach, a broken data model, a misread requirement, anything whose fix
rewrites code across several sections. Report only those. The deep dive's
other findings wait for their sections' reviews.

Where there are none, say so in a line and move on. Otherwise, weigh how much
rework they add up to and put the choice to the user as a numbered choice, as
[shared/choices.md](../shared/choices.md) sets out:

- **Hand back** - the rework is substantial enough for the author to do it.
  Hold every fundamental problem as step 6 holds an author's finding, and run
  the `review-pr` skill ([review-pr/SKILL.md](../review-pr/SKILL.md)) to post
  them. Its submit step hands the PR back to the author, and the pass ends
  there, with no sections.
- **Fix first** - work them as [shared/findings.md](../shared/findings.md) sets
  out before sectioning, holding any only the author can settle as step 6 does.
  Each fix counts among the fixes of the section its files fall in, for that
  section's documentation.

### 4. Section the branch, then summarise it

Work out the areas and their sizes as the `orient` skill
([orient/SKILL.md](../orient/SKILL.md)) does, without writing its output. Split
the scope into sections as the **Sections** in
[shared/reading-order.md](../shared/reading-order.md) set out, in the **Order**
there.

Open with a succinct summary, enough for the user to judge whether to change
the order and nothing more:

- A sentence or two on what the branch does, linking the PR.
- A bullet per section, in review order: its name, what it covers in a clause,
  its rough size, and why it sits there (what it builds on, what builds on it,
  or that the PR's suggested order puts it there). Generated and mechanical
  churn gets one bullet.
- The file count, checked as below.

Don't ask where to begin: start on the first section after the summary, leaving
the user to redirect.

The user ticks off each file in the PR's **Files changed** tab as they go, so
the sections between them cover every file that tab lists, narrowed to the
scope. Take the list from `git diff --name-status origin/<base>...HEAD` rather
than from what the net diff reads: lockfiles, generated files, renames and
deletions all count. Before the first section, check that the files across the
sections add up to the list's count, and say the count with the order.

### 5. Review the section

Run every pass in [shared/review-passes.md](../shared/review-passes.md) over the section's
files, checks included. A finding in another section's files waits for that
section's turn. One outside the scope is an aside for the report.

### 6. Work its findings

Take the section's findings one at a time, as
[shared/findings.md](../shared/findings.md) sets out.

Once the first section opens, close every message up to the report with where
the pass stands, as the section and finding numbers alone
(`Section 1/8, Finding 2/5`) on the last line, leaving their names out. The
messages before it, through steps 2 to 4, end without one. Keep it on a reply that only answers a tweak
inside the finding too, so the user never has to work out which finding is
open.

A finding only the author can settle isn't worked here. This covers a
question about what they intended, or a problem that's theirs to rework rather
than a final pass's to fix. Say so when it comes up, then hold it with its file,
lines and point in a line. The comment's wording is agreed later, in
`review-pr`.

### 7. Look through the section

Once a section's last finding is ticked off, commit its held stylistic fixes as
one commit, following the `commit` skill ([commit/SKILL.md](../commit/SKILL.md)).
Reaching the look-through is the go-ahead for that commit, so don't offer it.
Then list the code the section covered, so the user can look through it for anything the review missed: each
file, with the line ranges in it where the section holds only part of the
file. Mark the files this section finishes, so the user knows which to tick
off on GitHub. Wait until they say they're done with it. A problem they raise
becomes a further finding of the same section, worked as step 6 sets out, and
the list comes round again once it's ticked off.

### 8. Document the section's fixes

Every fix the section made is a mistake nothing steered the branch's author
away from. Run the `documentation` skill ([documentation/SKILL.md](../documentation/SKILL.md))
over them, so the agent docs steer the next author to the fixed version first
time and the next final pass has less to fix.

- Scope it to the section's fixes, committed and held, rather than the branch.
- Take each fix through its **Corrections to another author's work**, whoever
  wrote the branch, so every fix ends on one of its cases.
- Leave its edits uncommitted beside the section's held fixes, and stay on them
  until the user says they're done, as for a finding.

### 9. Next section, back to 5

Offer to run the `update-pr` skill ([update-pr/SKILL.md](../update-pr/SKILL.md)),
which commits the section's doc edits and any fixes still held before it
pushes, so a yes commits them now rather than at the report. Wait for the answer, then move
on as the **Sections** in
[shared/reading-order.md](../shared/reading-order.md) set out.

### 10. Report

The user watched every finding, so the report names things rather than
explaining them, as [shared/succinct.md](../shared/succinct.md) sets out:

- The files from the list that no section finished, if any.
- What was fixed, a line per fix by section: committed or held, and its doc
  edit.
- The findings dropped, held for the author, and asides, a line each.
- Where step 2 retargeted the PR, the open PRs based on its branch
  (`gh pr list --base <head>`), which need rebasing onto it.

Close with the offers that apply, together. Leave pushing to the user.

- Commit the held stylistic fixes as one commit.
- Land the part step 2 found standing alone, with the `land-first` skill
  ([land-first/SKILL.md](../land-first/SKILL.md)).
- Post the held findings with the `review-pr` skill
  ([review-pr/SKILL.md](../review-pr/SKILL.md)), whose submit step hands the PR
  back to the author.
- Where nothing was held, in propertywerks, move the PR to
  `[PR] ready for merge` once the pass's fixes are pushed, as
  [shared/pr-labels.md](../shared/pr-labels.md) sets out, leaving the assignees
  and reviews alone.
- Where another author wrote the branch, summarise the pass for them with the
  `changes` skill ([changes/SKILL.md](../changes/SKILL.md)).
