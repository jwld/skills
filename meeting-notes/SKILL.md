---
name: meeting-notes
description: >-
  Turns pasted meeting notes into GitHub updates: a comment on the issue a
  topic builds on, listed in that issue's outstanding work and flagged on its
  PR, and a new issue for new work. Use when the user pastes meeting notes and
  wants them recorded on GitHub.
---

# Meeting notes

Record each topic in the notes on the issue it belongs to, since a long PR
buries what's posted on it. Work that builds on an open issue goes on that
issue, new work gets an issue of its own, and the rest (priorities, business
context, decisions already acted on) stays out of GitHub.

The request is the go-ahead to post, so don't ask again before each one.

## Workflow

```
- [ ] 1. Sort the topics
- [ ] 2. Read the existing work
- [ ] 3. Comment on the issue
- [ ] 4. Add the items to the issue's outstanding work
- [ ] 5. Flag them on the PR
- [ ] 6. Open an issue for new work
- [ ] 7. Report
```

### 1. Sort the topics

Give every topic in the notes one destination:

- **Existing issue** - it adds to or changes work an open issue already holds,
  and has to land before that work's PR merges. Find open PRs with
  `gh pr list --state open` and their issues as the `update-issue` skill's
  **Find the issue** step sets out ([update-issue/SKILL.md](../update-issue/SKILL.md)),
  or the issue alone with `gh issue list --state open`. A PR with no issue
  gets one first, as the `open-pr` skill's **Find or open the issue** step
  sets out ([open-pr/SKILL.md](../open-pr/SKILL.md)).
- **New issue** - work nothing open covers yet, including a follow-up to open
  work that can wait until its PR merges.
- **Not recorded** - context, priorities and discussion with no work in it.

Follow the user's mapping where they give one, including what to leave out.
Where they don't, put the proposed mapping in one `AskQuestion` before posting
anything.

Topics can split across headings: an item under one heading can belong with
another's destination (a chatbot idea under a communication heading goes in the
communication issue when it depends on that work).

The comment names the meeting date as `Ddd D Mon` (`Tue 13 Oct`). Take it from
the user or the notes, and ask when neither gives it. It's often not today.

### 2. Read the existing work

Before wording anything, read the issue, and where it has a PR, that PR's
description and the code on its branch (`git show origin/<branch>:<path>`) for
each item it touches. Then for each item:

- Say what changes from what's there, quoting the current behaviour where the
  item revises it (the copy a message carries now, the case a prompt sends
  elsewhere). An item the PR already half does is a change, not an addition.
- Name existing code the work can reuse, such as a field that already works out
  the value.
- Check any claim about what the app does or doesn't do against the code before
  writing it.

### 3. Comment on the issue

`gh issue comment <number> --body-file -`, passing the body as
[shared/gh-body.md](../shared/gh-body.md) sets out, and opening with the meeting
date:

```
From the meeting on Tue 13 Oct, four additions for the chatbot:

- **Say we'll reply by email when escalating.** `handoffCopy` currently says "We will get back to you as soon as possible" - it should say the reply will come by email.
```

One bullet per item, led by a bold imperative, then what changes and why in a
sentence or two, written in full lines rather than wrapped.

### 4. Add the items to the issue's outstanding work

List the comment's items in the issue's outstanding section, laid out as the
`update-issue` skill's **Outstanding** sets out: the comment linked, then one
unticked checkbox per item, a line each. On an issue someone else wrote, that
section sits in the agent write-up, as `update-issue`'s **Someone else's
issue** sets out, and the rest of their body stays as it is.

Add to the section when it already exists. Write the whole body back with
`gh issue edit <number> --body-file -`, built from the current body so nothing
else changes.

### 5. Flag them on the PR

Where the issue has an open PR the items block, give its description the
outstanding-work notice the `write-up-branch` skill sets out
([write-up-branch/SKILL.md](../write-up-branch/SKILL.md)), unless it's already
there. Write the whole body back with `gh pr edit <number> --body-file -`,
built from the current body so nothing else changes.

Edit the description only when it's yours: its last editor, or its author
where nobody has edited it, is the current `gh api user` login
(`gh api graphql` for `author { login }` and `editor { login }`). Leave anyone
else's PR unflagged.

### 6. Open an issue for new work

Follow the `open-issue` skill ([open-issue/SKILL.md](../open-issue/SKILL.md)). Meeting
work is a backlog item, so leave it unassigned unless the user says it's theirs.

The body is the issue's `## Problem`, kept as prose, a short paragraph per
idea:

- Lead with the problem and the evidence behind it, naming who found it.
- Then the ideas from the notes, keeping a concrete example they gave.
- Link a related issue or PR, and say what the app would need first where
  something the idea relies on doesn't exist yet.

### 7. Report

Give the URLs: each comment, each issue whose outstanding work changed, each PR
that gained the notice, and each new issue. Say what each one records in a
line, and name anything left unrecorded.
