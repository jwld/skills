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
agrees to is fixed and committed in the working tree rather than posted. GitHub
stays read-only apart from the closing label: no comment, review, reply or
push. A finding only the author can settle is held for `review-pr` to post.

## Scope

When the user names a component or piece of code (`/final-pass TaskForm`), the
pass covers only the branch's changes to it: the paths it lives in. Otherwise
it covers the whole branch. Where the named code has no changes on the branch,
say so and ask whether to pass over it as it stands.

## Workflow

```
- [ ] 1. Open the branch
- [ ] 2. Orient, then section the branch
- [ ] 3. Review the section
- [ ] 4. Work its phases
- [ ] 5. Look through the section
- [ ] 6. Document the section's fixes
- [ ] 7. Next section, back to 3
- [ ] 8. Report
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

### 2. Orient, then section the branch

Follow the `orient` skill ([orient/SKILL.md](../orient/SKILL.md)), then split the
scope into sections as the **Sections** in
[shared/reading-order.md](../shared/reading-order.md) set out, in the **Order** there.
Don't ask where to begin: say the order and start on the first section, leaving
the user to redirect.

The user ticks off each file in the PR's **Files changed** tab as they go, so
the sections between them cover every file that tab lists, narrowed to the
scope. Take the list from `git diff --name-status origin/<base>...HEAD` rather
than from what the net diff reads: lockfiles, generated files, renames and
deletions all count. Before the first section, check that the files across the
sections add up to the list's count, and say the count with the order.

### 3. Review the section

Run every pass in [shared/review-passes.md](../shared/review-passes.md) over the section's
files, checks included. A finding in another section's files waits for that
section's turn. One outside the scope is an aside for the report.

### 4. Work its phases

Take the section's findings one phase at a time, as
[shared/phases.md](../shared/phases.md) sets out.

Close every message from here to the report with where the pass stands, as the
section and phase numbers alone (`Section 1/8, Phase 2/5`) on the last line,
leaving their names out. Keep it on a reply that only answers a tweak inside the phase too, so the
user never has to work out which phase is open.

Stay on a phase once its fix lands and its commit is offered, until the user
says they're done with it, since they may still be working on it in the tree.
Only then tick it off and open the next phase, the next section or the report,
rather than moving on when the fix is in.

A finding only the author can settle doesn't become a phase. This covers a
question about what they intended, or a problem that's theirs to rework rather
than a final pass's to fix. Say so when it comes up, then hold it with its file,
lines and point in a line. The comment's wording is agreed later, in
`review-pr`.

### 5. Look through the section

Once a section's last phase is ticked off, list the code the section covered, so
the user can look through it for anything the review missed: each file, with
the line ranges in it where the section holds only part of the file. Mark the
files this section finishes, so the user knows which to tick off on GitHub. A
file split across sections finishes with its last. Wait until they say they're
done with it. A problem they raise becomes a further phase of
the same section, worked as step 4 sets out, and the list comes round again
once it's ticked off.

### 6. Document the section's fixes

Every fix the section made is a mistake nothing steered the branch's author
away from. Run the `documentation` skill ([documentation/SKILL.md](../documentation/SKILL.md))
over them, so the agent docs steer the next author to the fixed version first
time and the next final pass has less to fix.

- Scope it to the section's fixes, committed and held, rather than the branch.
- Take each fix through its **Corrections to another author's work**, whoever
  wrote the branch, so every fix ends on one of its cases.
- Leave its edits uncommitted beside the section's held fixes, and stay on them
  until the user says they're done, as for a phase.

### 7. Next section, back to 3

Offer to run the `update-pr` skill ([update-pr/SKILL.md](../update-pr/SKILL.md)),
which commits the section's held fixes and doc edits before it pushes. Wait for
the answer, then move on as the **Sections** in
[shared/reading-order.md](../shared/reading-order.md) set out.

### 8. Report

- Confirm that a section finished every file in the list, naming any it didn't.
- Offer the held stylistic fixes as one commit, if any are waiting.
- List what was fixed, by section, each with whether it was committed and the
  doc edit it led to, if any.
- List the phases the user dropped, and the asides the passes turned up.
- List the findings held for the author, and offer to post them with the
  `review-pr` skill ([review-pr/SKILL.md](../review-pr/SKILL.md)). Its submit step
  writes the summary and hands the PR back to the author.
- Where nothing was held, in propertywerks, ask whether to move the PR to
  `[PR] ready for merge`. On a yes, and once the pass's fixes are pushed, move
  it as [shared/pr-labels.md](../shared/pr-labels.md) sets out, and leave the
  assignees and reviews alone.
- Where another author wrote the branch, offer to summarise the pass for them
  with the `changes` skill ([changes/SKILL.md](../changes/SKILL.md)). Leave pushing
  to the user.
