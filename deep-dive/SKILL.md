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
`~/.cursor/skills/shared/net-diff.md` sets out.

## Pending review

Where the branch has a PR, read your pending review's comments before the
review, so the findings build on them. A pending review is visible only to its
author, so these are points the user has drafted but not yet sent.

```bash
gh api graphql -f query='
  query($owner: String!, $repo: String!, $pr: Int!) {
    repository(owner: $owner, name: $repo) {
      pullRequest(number: $pr) {
        reviews(first: 1, states: PENDING) {
          nodes {
            comments(first: 100) {
              nodes { path line body }
            }
          }
        }
      }
    }
  }' \
  -f owner=OWNER \
  -f repo=REPO \
  -F pr=NUMBER
```

## Logic review

Run the **Logic** and **Verify before reporting** passes in
`~/.cursor/skills/shared/review-passes.md` over the diff. The other passes
belong to the reviews that ask for them.

## Output

- Report the logic findings and nothing else. The checks behind them stay out of
  the write-up: no list of what was verified and found fine, no typecheck / lint
  / test summary, no recap of the review process.
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
- Keep each one to the few lines it takes to act on, with no restating of the
  fix in prose once it's clear.
- Close by offering to fix the logic findings.
