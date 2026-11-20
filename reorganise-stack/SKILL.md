---
name: reorganise-stack
description: >-
  Retargets at master every PR in a feature branch's stack that can land
  alone, and relinks the stack on the feature branch for the rest. Use only
  when the user runs /reorganise-stack.
disable-model-invocation: true
---

# Reorganise a stack

A stack builds up to a **feature branch**: a branch with a PR of its own into
the default branch, held back until the whole feature is ready. A PR in the
stack that could land on the default branch alone shouldn't wait for the
feature. This skill retargets every such PR at the default branch, and relinks
the stack for the PRs that still need the feature branch.

A GitHub stack is only for PRs going into a feature branch. A PR whose parent
is going into the default branch is chained onto it, its base set to the
parent's branch, and never linked as a stack.

It changes PR bases and stacks on GitHub, and leaves every branch as it is. A
retargeted PR's diff carries whatever its branch holds that its new base
doesn't, until the branch is rebased when its work is picked up.

The user agrees the plan before anything changes. Their yes covers the
unstack, base changes and relinks the plan names, and nothing beyond it. Every
chat message is succinct, as [shared/succinct.md](../shared/succinct.md) sets
out.

## Workflow

```
- [ ] 1. Map the stack
- [ ] 2. Assess each PR
- [ ] 3. Plan the new shape
- [ ] 4. Publish
- [ ] 5. Report
```

### 1. Map the stack

Start from the current branch's PR, or the PR the user names.

- Read its GitHub stack off the REST API, since `gh stack view` sees only
  stacks tracked locally:

  ```bash
  gh api repos/<owner>/<repo>/pulls/<number> --jq .stack
  ```

  That gives the stack's `number`, which `gh stack` commands take, and its
  trunk (`base.ref`), the feature branch. The feature PR (`gh pr list --head
  <trunk>`) sits outside the stack.
- Walk up from the trunk with `gh pr list --base <head>` until a branch has no
  PR on it, recording each PR's number, head and base.

### 2. Assess each PR

Bottom up, from the PR's diff and description:

- **Leans on** - name each thing it uses from the PRs below it: a helper, a
  type, a migration, a renamed symbol, a file only they add. Leaning only on
  PRs bound for the default branch doesn't hold a PR back, since it can land
  there stacked above them.
- **Ships alone** - name anything that would reach users half-built on the
  default branch without the PRs above it, or that makes behaviour worse until
  they land.

Give each PR a verdict, **default branch** or **feature branch**, with the
reason in a clause. The order holds: a PR that leans on one staying on the
feature branch stays there too.

### 3. Plan the new shape

Two chains, each keeping the stack's order:

- **Default chain** - the bottom PR targets the default branch, and each other
  one the PR below it. It's left unlinked, however many it holds.
- **Feature chain** - the bottom PR targets the feature branch, and each other
  one the PR below it, linked as a stack on the feature branch as trunk.

Lead with the verdicts, a line per PR, then the two chains, then each PR whose
diff will carry work from below it until its branch is rebased. Put it to the
user as a numbered choice, as [shared/choices.md](../shared/choices.md) sets
out, and wait. Where every PR stays where it is, say so and stop.

### 4. Publish

In this order, since GitHub refuses a stacked PR's base change ("Cannot change
the base branch because the pull request is part of a stack"):

1. `gh stack unstack <stack number>`. It takes the whole stack, and leaves
   every PR open.
2. `gh pr edit <number> --base <new base>` for each PR whose base changed.
3. `gh stack link --base <feature branch> <feature chain>`, passing PR numbers
   bottom to top. A branch name makes `link` push it.
4. Read `.stack` off each PR again to confirm the new shape.

### 5. Report

- Each chain, its PRs bottom to top, the feature chain linked as a stack.
- The PRs whose diff carries work from below until their branch is rebased.
- PR descriptions that name an old base or stack position, offered to the
  `update-pr` skill ([update-pr/SKILL.md](../update-pr/SKILL.md)).
