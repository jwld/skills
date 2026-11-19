---
name: commit
description: >-
  Writes git commit messages in the user's preferred style (an imperative title
  plus short bullets, never a prose body). Use whenever creating or amending a
  commit, including when the user just says "commit", and when the user moves
  on to a separate idea while finished work is still uncommitted.
---

# Commit

Unstage anything already staged before reading the diff (`git restore --staged`).
Start from the working tree.

Follow the normal commit workflow. This skill governs the message, when to
amend HEAD instead of opening a new commit, and when to commit finished work
before a new idea (**When the user moves on**).

## Branch

- In the PropertyWerks repo (`~/GitHub/pwerks/propertywerks`), never commit on
  `master`. Switch to a new branch first (`git switch -c`), named by the repo's
  `.cursor/rules/branch-naming.mdc`, then commit there.
- On any other branch, commit where you are.

## When the user moves on

The user moving on to a new request is the go-ahead to commit finished work
still in the tree, when that work is a separate idea from the new request.
Commit it before starting the new request.

Separate means the new request would be its own commit under **Content**. A
continuation of the same idea stays in the tree.

- Commit the finished idea being left behind, and only its changes. Unrelated
  uncommitted work stays out of the commit.
- A half-done edit, work the user is still steering, and leftover scrap stay
  uncommitted.
- Where the outstanding work holds more than one finished idea, commit each as
  **Content** sets out, in that order.
- Then do the new request.

## Message shape

- Title, blank line, bullets. Never a prose paragraph between them.
- Bullets are one short line each, readable at a glance.
- Keep reasoning out of the bullets (no trailing "so that" or "which means"
  clauses).
- Drop the bullets entirely when the title already says everything.

## Title

- Imperative, sentence case, no full stop.
- No `feat:` / `fix:` / scope prefixes.

## Content

- Describe the change, so renames, moves and extractions are all fair game.
- One commit per idea. Group the diff into ideas first, then commit them so
  each builds on the one before it: the helper before its call sites, the
  rename before the code written against the new name.
- An idea stands on its own: a correctness fix, a duplication drop, an
  import-style sweep. Shared files or a shared feature are not one idea.
- Bullets detail that one idea. If each bullet would still make sense as a
  title, they are separate commits.
- Doc changes (skills, `AGENTS.md`, `CONTEXT.md`, other Markdown docs) bundle
  into one commit, a bullet per change, however many ideas they hold. A doc
  change tied to a code change rides in that change's commit instead.
- That bundling is for docs caught up alongside code. In the skills repo
  (`~/.cursor/skills`), and on a branch whose changes are all docs, one commit
  per idea still holds.

## Amend

Amend HEAD instead of a new commit when all of these hold:

- The change is the same idea as HEAD (leftover from that commit, or a small
  follow-up on the same files and thought)
- HEAD was created by you in this conversation
- The commit has not been pushed

Rewrite the message if the extra change makes the old title incomplete. If any
of those fail, make a new commit instead.

## Passing the message to git

Never write the message with the file-writing tool (that file lands in the
user's review). Build it inside the commit command instead, so it never exists
as an edit:

```
printf '%s\n' "Title line" "" "- first bullet" "- second bullet" > /tmp/commit-msg.txt && git commit -F /tmp/commit-msg.txt && rm /tmp/commit-msg.txt
```

- Keep `printf` and `rm` in the same call as the commit, so nothing is left
  behind when the turn ends.
- Never put a heredoc inside `$(...)`, where the shell misparses an apostrophe
  in the message.
- A single-line message needs none of this (just `git commit -m`).

## Example

```
Hold a menu node's children on nodes rather than rows

- Rename MenuNode.rows to nodes across the builders and convertTreeToLines
- Resolve the GraphQL rows field from nodes, on the type so it covers every level
- Leave the schema, generated types and front-end untouched
```
