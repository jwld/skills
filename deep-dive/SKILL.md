---
name: deep-dive
description: >-
  Deep dive over the changed logic on a finished branch, listing potential
  errors and inconsistencies. Use only when the user runs /deep-dive.
disable-model-invocation: true
---

# Deep dive

A single pass over the changed logic on the finished branch. The findings stay
for the user to act on. Make no code edits until they say which.

## Scope

Work from the net diff vs the parent, gathered as
[shared/net-diff.md](../shared/net-diff.md) sets out.

## Pending review

Where the branch has a PR, read your pending review's comments before the
review, as [shared/pending-review.md](../shared/pending-review.md) sets out,
so the findings build on them.

## Logic review

Run the **Logic** and **Verify before reporting** passes in
[shared/review-passes.md](../shared/review-passes.md) over the diff. The other passes
belong to the reviews that ask for them.

## Output

- Report the logic findings and nothing else, succinct as
  [shared/succinct.md](../shared/succinct.md) sets out.
- Leave out a finding a pending comment already makes. Where one goes further
  than a pending comment, name the comment it builds on.
- Where the review shows a pending comment is wrong, or the code it's about has
  changed since, say so in a short closing list, so it can be fixed before the
  review is submitted.
- Say so explicitly when nothing needs changing, or nothing beyond what the
  pending comments already raise.
- Order findings by consequence, and make clear which are blockers and which are
  nits.
- Give each finding a file and what is actually wrong, not a category.
- Close by offering to fix the logic findings.
