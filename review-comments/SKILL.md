---
name: review-comments
description: >-
  Rules for a code comment, and a review of every comment in a file or
  directory. Use when writing or editing a comment or doc block, or when the
  user runs /review-comments or names a file or directory of comments to check.
---

# Review comments

This file is the rule for what a comment says and for a pass over existing
ones. A repo `AGENTS.md` that states a comment format (block style, wrap width,
where a link goes) is the format for that repo. Where it is silent, use the
format below. Where it disagrees about what earns a comment, this file wins.

Two ways in:

- **Writing.** A comment is being added or edited. Apply the rules to it, and
  to any comment in the same file the edit makes stale. Stop there.
- **Reviewing.** A file or directory was named, or the user ran
  `/review-comments`. Review every comment in that path. Ask for the path when
  none was named.

## What earns a place

- A comment earns its place by saying what the code can't: a constraint, an
  external quirk, the reason behind a choice that looks wrong without it. A
  quirk that only lets the code work the way a reader already assumes (a
  database allowing the connection the code obviously makes) earns none, since
  only someone who knows the quirk would have asked.
- A reason earns its place only where undoing the code would fail silently (a
  wrong result that reads as a right one). Where undoing it fails loudly (a
  cast the typecheck needs, a guard a spec covers), the failure explains it, so
  leave the comment out.
- A doc block on a function too long to take in at a glance earns its place by
  saying what the function does, in the fewest words a person needs to see
  that. A short declaration whose name and body already say what it is (a type
  alias, a one-line constant) takes none.
- A doc block on a function opens with what the function does, whatever its
  length. A caveat or constraint follows that sentence rather than standing in
  for it.
- Where any other comment explains what the code does, the fix is the code.
  Rename the thing or pull the step out, then drop the comment.
- Say what a function rejects rather than naming the error it throws. The
  throw sits a few lines below, so the code is noise in the comment.
- Judge a comment sentence by sentence. A sentence restating what the code
  already shows (a value, a media query, a token the declaration names) comes
  out, even when the sentence beside it earns its place.
- Give a reason once, in the comment beside the code it constrains. Where
  another comment already carries it, leave it out of this one.
- In the present tense of the code as it stands. The formatter wraps it.
- A comment that overstates what the code does, walks the steps, or describes
  the refactoring journey comes out.
- A comment that says the code does something it doesn't is wrong. Correct it
  or delete it ahead of any trim. A stale comment is worse than none.
- A comment that restates a rule already in a loaded `AGENTS.md` stays only
  when this line looks wrong without the local reason, or a person reading the
  file (who won't have that `AGENTS.md` open) would misread the line without
  it, as with a constraint imposed from elsewhere. Decide per comment, and
  keep only the sentence that reader needs, leaving the agent-facing detail to
  the doc.
- A comment that states a convention holding beyond this spot is an agent doc
  rule in the wrong home. Move it to the nearest `AGENTS.md` as the
  `review-docs` skill sets out, and apply the rule above to what's left.

## How it reads

A comment is for a person meeting the file for the first time, so readability
beats brevity.

- Say what this code does, then why. A comment that describes what some other
  function does, or an approach this code doesn't take, leaves the reader to
  work out what that means here. A warning against that approach goes in the
  nearest `AGENTS.md`.
- Name the code each sentence is about as the code names it (the CSS property,
  the constant, the field), so the reader can match it to a line. A comment
  above a style block names every property it explains (`position: absolute`
  keeps the window out of the stack's flow, so...).
- Give the consequence the reader cares about (the user is signed out, a
  payment is taken twice) rather than the mechanism's internal names. Name
  another function only where the reader needs to go and look at it.
- Plain words over shorthand. A term the reader would have to look up is
  rewritten as what it means.
- Two short sentences over one long one. Split a sentence that needs rereading
  to follow its "so" or "which".

## Format

Where the repo states none of its own:

- A doc block (`/** ... */`) only on a named declaration, directly above it,
  with a blank line between the block and the declaration.
- A `//` line for everything else, including a note inside a function.
- A link sits in a doc block as an `@see` tag.
- A comment that runs past one line reads as proper sentences: a capital, a
  full stop.
- A wrapped comment whose last line holds only a word or two is reworded to
  fit one line fewer.
- A bare `/* ... */` block is only a file-level `/* eslint-disable */`.

Leave an `eslint-disable` line, a license header, and a comment inside a string
alone.

## Review

1. Read every comment in the path, and the code it sits on. Judge it against
   that code.
2. Edit in this order: wrong, then no longer needed, then hard to read, then
   overlong. Leave a comment that states a constraint the code can't.
3. Apply the edits. Don't commit.

Done when every comment in the path has been judged and every edit matches the
rules above.

## Report

When another skill called this, use that skill's report. Otherwise report as
[shared/doc-report.md](../shared/doc-report.md) sets out.
