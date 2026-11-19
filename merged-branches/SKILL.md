---
name: merged-branches
description: >-
  Lists which local branches have a merged pull request, then asks to confirm
  a force delete of exactly those branches. Use when the user asks which of
  their local branches have been merged, or confirms that force delete.
---

# Merged local branches

List the local branches whose pull request has been merged. Then ask to
confirm a force delete of exactly the branches in that list.

A squash merge writes a new commit on the base, so the branch tip is not an
ancestor of the base. Decide from the merged pull request's head branch name.

The delete set is the branch names written in the confirmation question.
Force-delete with `git branch -D --`, one of those names at a time, and only
after the user confirms.

## Workflow

```
- [ ] 1. Confirm the repo
- [ ] 2. Look up each local branch
- [ ] 3. Reply
- [ ] 4. Ask to confirm
- [ ] 5. Force-delete the confirmed names
```

When the user is confirming a question already asked in this conversation, do
step 5 only.

### 1. Confirm the repo

`gh repo view --json nameWithOwner,defaultBranchRef` has to succeed. Stop and
say so when it does not: this repo is not a GitHub repo `gh` can see.

Done when you have the default branch name.

### 2. Look up each local branch

Run this from the repo root. It skips the default branch and prints one line
per remaining local branch. Pass the branch name alone to `--head`.

```bash
default=$(gh repo view --json defaultBranchRef --jq .defaultBranchRef.name)
git for-each-ref --format='%(refname:short)' refs/heads/ | while read -r branch; do
  [ "$branch" = "$default" ] && continue
  result=$(gh pr list --head "$branch" --state merged \
    --json baseRefName,mergedAt,number,url --limit 1 \
    --jq 'if length == 0 then "" else .[0] | "#\(.number)\t\(.mergedAt)\t\(.baseRefName)\t\(.url)" end')
  printf '%s\t%s\n' "$branch" "$result"
done
```

An empty result means that branch has no merged pull request. `--limit 1` is
the latest one when the name was reused.

Done when every local branch except the default has a line.

### 3. Reply

List only the branches whose result is non-empty, most recently merged first.
One line each:

```
- <branch> - #<number>, merged <YYYY-MM-DD> into <base> (<url>)
```

Add `checked out` at the end of the line for `git branch --show-current`.

When none matched, say none of the local branches have a merged pull request.

End with how many local branches were checked, the default excluded.

### 4. Ask to confirm

When the reply listed one or more branches, ask to confirm a force delete of
those branches. Repeat every branch name in the question, except the
checked-out branch, which stays. Say that it stays.

This turn ends at the question. Step 5 is the turn after the user confirms.

When none matched, or the only match is the checked-out branch, there is
nothing to delete.

Done when the question names each listed branch other than the checked-out
one, or when there is nothing to delete.

### 5. Force-delete the confirmed names

Run this only after the user confirms the question from step 4.

The delete set is the branch names written in that question, minus any name
the user then drops. That written list is the whole set: do not look branches
up again, and do not add a name. A name absent from the question stays,
including the default branch and the current checkout.

For each name in the set, and for no other name:

```bash
git branch -D -- "$name"
```

One name per command. The name is a literal from the question, not the output
of another git command.

Done when every name in the confirmed set was deleted or the command refused
it, and the reply says which.
