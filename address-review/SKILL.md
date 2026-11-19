---
name: address-review
description: >-
  Works through a pull request's review one point at a time, judging whether
  each point is valid with the user before fixing it and pausing for review.
  Use when the user asks to address a PR review or its comments.
---

# Address a review

Take a review's points one at a time: judge it, implement it, stop for review,
commit it, and only then move to the next. The weight sits in
the judging: a point is a claim about the code, and it gets tested before
anything is built on it. The reviewer can be wrong, half right, or right about the
problem and wrong about the fix.

GitHub is read-only until all the work is done: no issue opened or edited, no
PR write-up or label changed, no reply posted, no thread resolved. All of that
happens in step 6.

## Workflow

```
- [ ] 1. Read the PR and every review
- [ ] 2. List the points - check the guesses
- [ ] 3. State the case, then number the options
- [ ] 4. Implement, then stop for review
- [ ] 5. Next point - back to 3
- [ ] 6. Wrap up - finish the work, then GitHub
```

### 1. Read the PR and every review

- Read the PR and its review bodies:

  ```bash
  gh pr view <url-or-number> --json \
    number,title,body,url,author,headRefName,baseRefName,state,labels,reviews
  ```

  Stop if it's merged or closed.
- Check out the head branch if you aren't on it, and pull, so the code you
  judge points against is what the reviewer will see next.
- Read every unresolved review thread with all its replies. The query returns
  resolved ones too, and their `isResolved` is all you need from them:

  ```bash
  gh api graphql -f query='
    query($owner: String!, $repo: String!, $pr: Int!) {
      repository(owner: $owner, name: $repo) {
        pullRequest(number: $pr) {
          reviewThreads(first: 100) {
            nodes {
              id isResolved isOutdated path line originalLine
              comments(first: 50) {
                nodes { author { login } body createdAt url }
              }
            }
          }
        }
      }
    }' -f owner=OWNER -f repo=REPO -F pr=NUMBER
  ```

- Read the PR's conversation comments too
  (`gh api repos/{owner}/{repo}/issues/{number}/comments`). Findings sometimes
  land there rather than in a review.

Read each review body in full. A long one is usually a list of findings, each
its own point, often with the reviewer's own lean beside it.

### 2. List the points - check the guesses

A point is one thing to act on or decide against: an unresolved thread, or one
finding from a review body or conversation comment. Two findings in one comment
are two points. A review-body finding that names a thread ("fixed by my comment
on X") folds into that thread's point, carrying the reviewer's lean with it.

Which points apply is often a judgment. Make a best guess on each:

- A resolved thread has been addressed or deliberately ignored, whichever it
  was. Leave it out without checking the code or second-guessing the call, and
  a review-body finding that folds into one goes with it.
- An outdated thread that's still unresolved stays in. The code moved, but the
  point may not have.
- A thread whose last reply says it's fixed, or that the reviewer accepted a
  decline, is probably done. Check the code before counting it out.
- Praise, summaries and questions already answered aren't points.
- Bot reviews (Bugbot and the like) count the same as people.
- Every review round is in scope unless a later one supersedes an earlier.

List the points in a line each, with the reviewer, where it lands, and the
reviewer's lean where they gave one. Take them in the order the PR lists them:
a review body in the order its findings are written, then the inline threads in
the order the query returns. Leave that order only when one point's fix has to
land before another's, and say so. Track them with the todo tool, one todo per
point.

The list names the points and nothing more: no verdicts or fixes yet. Each one
is judged when its turn comes, against the code as the earlier points left it.

Where any inclusion, exclusion or merge was a guess, say what made each one a
guess and why you leaned the way you did, then end that message with a numbered
choice and wait for the number. One choice per guess, your guess first and
marked `(Recommended)`. Where nothing was a guess, carry on to the first point.

### 3. State the case, then number the options

Judge this point alone. The next point waits until this one is committed or
decided against. The fix is discussed only once the user agrees there is
something to fix.

Never use `AskQuestion`. The case, the verdict and the options are one chat
message, and a reply is a number. Thinking doesn't count, because the user
never sees it: if the quote, the verdict and the numbered options are not in
the chat message, this step was skipped.

**Validity** - before discussing anything, test the point against the code on
the current head:

- Restate the claim in a sentence, with the file and lines it lands on.
- Check whether it still holds. Trace the path, and reproduce it where a run is
  cheap (a query, a test, a script). Outdated threads are often already fixed.
- Where the point is about what the UI does and the code can't settle it (a
  layout, a flow, how something feels on a phone), the message quotes the
  comment and gives the steps to reproduce in a few lines, then numbers what
  they might find, such as it happens or it doesn't. The verdict waits for the
  number.
- Judge the problem and any suggested fix separately. A real problem can come
  with the wrong fix.
- Weigh the reviewer's lean ("not a problem IMO", "maybe worth adding?") as
  evidence, not as the verdict. Agree or disagree with it in so many words.
- Weigh scope: does this belong in this PR, or in a follow-up issue?

**The case**, and nothing else:

1. The review comment in full, quoted, with who wrote it and where it lands.
   Quote it whole even when it's long; the length bound below doesn't cover
   the quote.
2. A short reason: the verdict (**valid**, **partly valid**, **not valid** or
   **already addressed**), one code reference to the lines that decide it,
   what you checked, and where you part from the reviewer. About 150 words,
   at most one code block. The user knows the PR, so skip background,
   narration of the diff, and hedges. On Opus, which runs long, hold to that
   bound hard: draft, then cut anything the decision doesn't turn on.

Then number how to respond, in the same message, recommendation first and
marked `(Recommended)`:

1. Fix it as suggested
2. Fix it another way
3. Move it to a follow-up issue
4. Decline

Stop and wait for the number. A point the user agrees needs no change in this
PR goes straight to step 5.

### 4. Implement, then stop for review

- Implement the response they chose, on the PR's head branch, and verify
  it as
  [shared/verify.md](../shared/verify.md) sets out.
- Stop for review: explain the change and point at the files that matter. Don't
  commit yet.
- On the go ahead, commit with the `commit` skill
  ([commit/SKILL.md](../commit/SKILL.md)). Leave pushing to the user; a commit is
  not a push.

### 5. Next point - back to 3

Tick the point off and go back to step 3 for the next one, saying which you're
moving to. The user can redirect.

The review is addressed when every point is ticked, not when the important ones
are.

### 6. Wrap up - finish the work, then GitHub

**Finish the work** - once every point is ticked, ask whether the user's happy
with the work across all of them. The work is done once they say so and every
fix is committed.

**Then GitHub**, each on the user's go ahead:

- Push and refresh the PR write-up and its issue with the `update-pr` skill
  ([update-pr/SKILL.md](../update-pr/SKILL.md)), when the user asks for it.
- In propertywerks, once the fixes are pushed, move the PR to
  `[PR] needs re-review` as [shared/pr-labels.md](../shared/pr-labels.md) sets
  out, so the reviewer knows it's back with them.
- Open a follow-up issue for each point moved to one, with the `open-issue`
  skill ([open-issue/SKILL.md](../open-issue/SKILL.md)).
- Draft a reply per point: what changed and the commit it landed in, or why it
  was declined or moved to an issue (with the link). A couple of sentences,
  neutral register, no thanks preamble. Show each rendered as plain paragraphs
  and then verbatim in a fenced block, since replies land publicly under the
  user's name. A reply citing a commit only makes sense once it's pushed, so
  post them after the push.
- Post a thread point's reply in its thread, then resolve that thread, whether
  the point was fixed, declined or moved to an issue:

  ```bash
  gh api graphql -f query='
    mutation($threadId: ID!, $body: String!) {
      addPullRequestReviewThreadReply(
        input: { pullRequestReviewThreadId: $threadId, body: $body }
      ) { comment { url } }
    }' -f threadId=PRRT_ID -F body=@- <<'EOF'
  The reply, as markdown.
  EOF

  gh api graphql -f query='
    mutation($threadId: ID!) {
      resolveReviewThread(input: { threadId: $threadId }) {
        thread { isResolved }
      }
    }' -f threadId=PRRT_ID
  ```

- Answer review-body and conversation points in one PR comment
  (`gh pr comment --body-file -`), quoting each finding above its answer.
