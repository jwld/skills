---
name: open-issue
description: >-
  Opens a GitHub issue with a type, labels and the right assignee. Use
  whenever creating a GitHub issue, including from inside another skill.
---

# Open an issue

Every issue gets a body, a type, labels for what it touches, and an assignee
decision. Check the remote owner first; the type and labels depend on it. Take
label names from the repo's own set (`gh label list`).

## Body

Every body opens with `## Problem`: why the work exists, which the issue keeps
for good. The rest depends on where the issue comes from.

- **From a planning flow** (the `phased-implementation` skill) - the body that
  flow writes, plan and all.
- **For a PR that has none** (the `open-pr` skill) - the
  work is already done, so the body is the `update-issue` skill's
  **Write-up** for one PR ([update-issue/SKILL.md](../update-issue/SKILL.md)), titled
  as the PR will be.
- **A bug** - the problem, a sentence or two. When the chat suggested a fix,
  follow it with `## Suggested fix`: the fix as suggested, naming the code it
  touches where the chat did. The plan comes later, from the planning flow, so
  the body stays prose.
- **Anything else** (a feature, a phase of a larger piece of work) - under
  `## Problem`, a simple summary of the work in a sentence or two, since there
  is no bug to describe. Then `## Plan` with a detailed breakdown of its scope
  as a bullet list, never `## Suggested fix`, which is for bugs.

## Type

Every issue carries one, whoever it's for.

- **propertywerks** - as the repo's `docs/conventions/github.md` sets out.
- **completionism** - the repo has no native types, so a `[t]` label is the
  type.
- **Any other repo** - a native issue type with `--type` where the owner has
  them (`gh api orgs/<owner>/issue-types`), otherwise the repo's type label.

When none fits, ask rather than open it untyped.

## Labels

A label for each area the work touches.

- **propertywerks** - as the repo's `docs/conventions/github.md` sets out.
- **completionism** - a `[p]` label for each project the work touches.

## Assignee

- **propertywerks** - as the repo's `docs/conventions/github.md` sets out.
- **Any other repo** - assign the user (`--assignee @me`) when the issue is for
  work they're doing or about to start. Leave it unassigned when it's a backlog
  item, an idea for later, or someone else's work.

## Create

Pass the body as [shared/gh-body.md](../shared/gh-body.md) sets out. Each command
below takes the body's heredoc after it.

```
gh issue create --title "..." --type Improvement --label "server" --assignee @me --body-file - <<'EOF'
gh issue create --title "..." --label "[t] refactor" --label "[p] server" --assignee @me --body-file - <<'EOF'
```

Give the issue URL.
