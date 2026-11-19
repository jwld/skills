---
name: review-pr
description: >-
  Reviews an open pull request top to bottom, or posts specific comments the
  user brings, agreeing each comment with the user and submitting the lot as a
  request-changes review. Makes no code edits. Use only when the user runs
  /review-pr.
disable-model-invocation: true
---

# Review a PR

A review of a pull request, posted as comments on GitHub. The working tree
stays untouched: no fixes, no docs sweep, no offer to fix, however obvious the
fix looks. Fixing is a separate pass the user asks for by name.

Labels and assignees are propertywerks-only. Check the remote owner, and skip
those steps anywhere else.

## Full review or specific comments

Before anything else, ask which this is. Then stop and wait.

- **Full review:** top to bottom, one section at a time, through every step
  below.
- **Specific comments:** the user has already been over the branch, by hand or
  with `final-pass`, and has points for the author. Skip steps 2, 3 and 5. The
  candidates for step 4 are the findings a `final-pass` held this session, plus
  any points the user gives. Read the code each one is about, so its anchor and
  claim hold up, and say so where one doesn't before drafting it.

Suggest specific comments first where a `final-pass` held findings this
session. Skip the question where the user has just accepted that pass's offer to
post them.

## Workflow

```
- [ ] 1. Open the review
- [ ] 2. Orient, then section the branch
- [ ] 3. Review the section
- [ ] 4. Discuss each comment, add the ones agreed
- [ ] 5. Next section, back to 3
- [ ] 6. Submit as request changes
```

### 1. Open the review

- Read the PR:

  ```bash
  gh pr view --json \
    number,title,body,url,author,assignees,headRefName,headRefOid,baseRefName,labels,state
  ```

  Stop if it's merged or closed.
- Check out the branch if you aren't on it, and fetch the base, so the diff and
  the files are real.
- Read the comments already on it
  (`gh api repos/{owner}/{repo}/pulls/{number}/comments`), so the review doesn't
  repeat a point someone has made.
- Swap the label for `[PR] in review` unless it's already there, removing
  whichever `[PR]` stage label the PR arrived with (usually `[PR] needs review`,
  or `[PR] needs re-review` on a later round, and the `gh pr view` output above
  says which):

```bash
gh pr edit --add-label "[PR] in review" --remove-label "[PR] needs review"
```

- Get the PR node id and your pending review, if you have one:

```bash
gh api graphql -f query='
  query($owner: String!, $repo: String!, $pr: Int!) {
    repository(owner: $owner, name: $repo) {
      pullRequest(number: $pr) {
        id
        reviews(first: 1, states: PENDING) {
          nodes { id }
        }
      }
    }
  }' \
  -f owner=OWNER \
  -f repo=REPO \
  -F pr=NUMBER
```

- Create the pending review the whole session's comments land in, when the query
  found none:

```bash
gh api graphql -f query='
  mutation($prId:ID!){
    addPullRequestReview(input:{pullRequestId:$prId}){ pullRequestReview{ id } }
  }' -f prId=PR_NODE_ID
```

GitHub allows one pending review per person per PR, so an existing one gets
reused rather than replaced (that's also how a part-finished review resumes in a
later session). Keep the review id (`PRR_…`) to hand: steps 4 and 6 both need
it.

### 2. Orient, then section the branch

Follow the `orient` skill ([orient/SKILL.md](../orient/SKILL.md)), then split the
branch into sections and ask where to begin, as the **Sections** in
[shared/reading-order.md](../shared/reading-order.md) set out.

### 3. Review the section

Run every pass in [shared/review-passes.md](../shared/review-passes.md) over the section's
files, checks included. An aside stays with the user and never becomes a
comment.

Then name the findings in a line each, so the user can see the shape of the
section and how many comments are coming, before step 4 takes them one at a
time.

### 4. Discuss each comment, add the ones agreed

One candidate at a time, most consequential first. For each:

- Say the file and the exact line range it will anchor to, then give the body
  twice: once rendered so it reads at the chat's width, then verbatim in a
  fenced block to copy from. A fenced block alone doesn't wrap, so a long line
  is unreadable. The user is approving text that lands publicly under their
  name.
- The rendered version keeps the body's own paragraph and bullet breaks.
  Reflowing it into one block is as hard to read as the unwrapped fence.
- Render it as plain paragraphs, not a blockquote. The chat collapses the blank
  lines inside a `>` block, which loses those breaks again.
- Wait. They may take it, reword it, downgrade it to a nit, or drop it.
- Add it to the pending review as soon as they agree, then move to the next.

**Bodies**

- One point per comment. Two points about the same line are two comments.
- State the problem, then the fix where one is worth naming, and stop. A couple
  of sentences carries almost every finding; spelling out each consequence, or
  what the fix tidies up downstream, is padding the author will skip.
- Head the finding by its kind, decided by asking whether anything goes wrong
  if the author ignores it (wrong data or behaviour, a documented rule broken),
  counting only what something can reach today rather than what a future change
  might:
  - `#### Problem` - yes. Fix before merge, or argue it out.
  - `#### Nit` - no. The code works and follows the rules, but there's a
    clearer or simpler way the author can turn down at no cost.
  - `#### Question` - you genuinely can't tell, never a softened problem.
- Follow a problem or nit with `#### Suggested solution` where a fix is worth
  naming.
- Write the fix for what the author can change. Where they also own the other
  side of a contract the code meets (a listener in another repo, say), offer the
  fix on either side rather than assuming that side is fixed.
- Say what's wrong and what it causes. The heading carries the kind, so add no
  `nit:` prefix.
- Cite a precedent by naming it, never by quoting it. A pasted `AGENTS.md`
  paragraph is fluff (the author can read the file, and the rule is the
  authority whether or not it's reproduced).
- Neutral register: this is someone's work to their face. No praise sandwich, no
  hedging, no narrating the diff back.

**Anchoring**

- `line` is a line number in the head commit's version of the file for
  `side: RIGHT`, and in the base version for `side: LEFT` (a deleted line). Take
  both from the diff hunks.
- A range adds `startLine:Int` (and `startSide:DiffSide`) to the mutation, with
  `line` as the end of the range.
- The line must sit inside a diff hunk (context lines count, anything further
  out is rejected). Where the real problem sits outside the diff, anchor to the
  nearest line in the hunk and name the line you mean.
- Anchors are relative to the head commit the review opened on. If the author
  pushes while the review is in progress, re-read the diff before anchoring
  anything more.
- Where the local branch has commits the PR head doesn't (the user's own fixes,
  not yet pushed), take lines from `gh pr diff`, not the local files. Where
  those fixes rewrote the lines a finding is about, ask whether to push first,
  so the comment doesn't point at code the fix has already changed.

```bash
gh api graphql -f query='
  mutation(
    $reviewId: ID!
    $path: String!
    $line: Int!
    $side: DiffSide!
    $body: String!
  ) {
    addPullRequestReviewThread(
      input: {
        pullRequestReviewId: $reviewId
        path: $path
        line: $line
        side: $side
        body: $body
      }
    ) {
      thread { id }
    }
  }' \
  -f reviewId=PRR_ID \
  -f path=PATH \
  -F line=NUMBER \
  -f side=RIGHT \
  -F body=@- <<'EOF'
The comment body, as markdown.
EOF
```

- `-F body=@-` reads the body from stdin, so a quoted heredoc keeps backticks,
  apostrophes and backslashes intact. Run it directly rather than inside
  `$(...)`.
- `-F` for typed values (`line`, `startLine`, the stdin read), `-f` for literal
  strings.

### 5. Next section, back to 3

Move on as the **Sections** in [shared/reading-order.md](../shared/reading-order.md) set
out.

### 6. Submit as request changes

- Draft the summary and show it before submitting. Wait for the go-ahead
  (submitting is public and can't be withdrawn).
- The summary is the verdict: what state the branch is in, what the blockers
  are, and what a next pass needs to cover. The comments carry the detail, so
  don't restate them.
- Submit, then move the PR to `[PR] changes requested` as
  [shared/pr-labels.md](../shared/pr-labels.md) sets out, and assign it back to
  its author (`author.login` from step 1), removing whoever else is assigned:

```bash
gh api graphql -f query='
  mutation($reviewId:ID!,$body:String!){
    submitPullRequestReview(input:{
      pullRequestReviewId:$reviewId,event:REQUEST_CHANGES,body:$body
    }){ pullRequestReview{ url state } }
  }' -f reviewId=PRR_ID -F body=@- <<'EOF'
The summary, as markdown.
EOF
```

```bash
gh pr edit --add-assignee AUTHOR --remove-assignee OTHER_ASSIGNEES
```

- Reply with the review URL and how many comments it carries.

Where the review turned up nothing, request changes is the wrong verdict: say
the branch looks sound and ask whether to approve, comment, or leave the review
pending.
