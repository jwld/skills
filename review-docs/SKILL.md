---
name: review-docs
description: >-
  Rules for an agent doc entry (`AGENTS.md`, `CONTEXT.md`, or a doc one of them
  points at), and a review of every entry in a file or directory. Use when
  writing or editing one, or when the user runs /review-docs or names a file or
  directory of agent docs to check.
---

# Review docs

This file is the rule for what an agent doc says, where it lives and how it
reads, and for a pass over existing ones. Where the repo documents its own
routing (a table of which file holds what), that routing wins over **Where it
goes**.

Two ways in:

- **Writing.** An entry is being added or edited. Apply the rules to it, and to
  any entry in the same file the edit makes wrong or duplicates. Stop there.
- **Reviewing.** A file or directory was named, or the user ran `/review-docs`.
  Review every entry in that path. Ask for the path when none was named.

## What earns a place

- A doc that says something the code doesn't do is wrong. Correct it or delete
  it ahead of any other edit: a stale rule misleads, where a missing one only
  fails to help.
- Check every claim against the source as it stands (commands, scripts, flags,
  stated invariants). The last author's verification expired when the code
  moved. Where the doc and the environment disagree, find out which one moved.
- Only patterns that will recur. A one-off decision goes in the PR write-up.
- Only what the code doesn't already make obvious.
- Only what the environment can't be asked. A restatement of a script, a config
  value or a directory listing is a copy that goes stale, and the lookup is one
  command away.
- Only what an agent can act on. A procedure only a person can run (a
  dashboard, a device, an account signup) goes in the repo's human docs where
  it has them, and nowhere otherwise.
- The reason behind a rule only where getting it wrong fails _silently_ (a wrong
  result that reads as a right one). Where the instruction already forces the
  right action and a mistake announces itself, state the rule and stop: the
  mechanism behind it changes nothing the agent does.
- Every sentence changes what the agent does versus its default. Where you
  can't name the behaviour a sentence causes, delete the whole sentence. Where
  you can, keep it however long, and cut only the words that don't change it. A
  copyable template earns its place.
- Phrase it as what to do, not what was done. Agent docs are read cold, by
  someone with no memory of this work, so a convention is needed on the
  thousandth edit as much as the first.
- State the target behaviour rather than banning the wrong one. A prohibition
  drags the forbidden thing into context and makes it more available, so a ban
  earns its place only as a hard guardrail, and sits beside the target.
- Prefer amending an existing rule over adding a second one beside it. Two rules
  covering the same ground age badly and start contradicting each other.
- Nothing that goes stale on the next change (no file inventories, no line
  references, no lists of current call sites).
- Delete a doc that has stopped carrying meaning, and the pointers to it,
  rather than leaving a stub.

## New patterns

A way of doing something the work introduced, documented nowhere yet, where
one call site is not enough for the next author to infer a rule from:

- Write it down as the rule to follow, so the work that comes after it matches
  rather than guessing.
- End the rule with `_(inconsistent)_` when the code doesn't yet follow it
  everywhere. Say where the code that predates it now diverges, and leave
  that code as it is. Bringing the rest of the repo up to date is a separate
  PR, not this branch's to carry. Drop the tag once the last of them is
  converted.

## Where it goes

- The root `AGENTS.md` loads on every request, so it holds only what applies
  across the whole repo, and earns harder pruning than any other file. A
  convention that holds in one directory tree belongs in that tree's nested
  `AGENTS.md`.
- Move a section that governs the files of one subfolder down into that
  subfolder's `AGENTS.md`, so a file holds only what its own folder needs. A
  nested file loads only for work inside its folder, so keep a rule at the
  lowest level covering every file whose edit should trigger it: a rule for
  editing files in two sibling folders stays in their parent.
- Domain vocabulary: the definition belongs in the repo's glossary where there
  is one, and the rule about using the entity stays in the `AGENTS.md` nearest
  the code, so a term is defined once and governed once.
- A nested `AGENTS.md` loads on its own when the agent works in its directory,
  so it needs no pointer. Any other doc does: point at it from a file that loads
  on its own, and fix the orphans you pass (a doc nothing points at only reaches
  whoever already knows it exists).
- Word a pointer with the branches that trigger it ("Read X before adding or
  changing A, B or C") rather than a bare "see also". The wording decides
  whether it fires.
- After moving material, re-read every pointer to it as a trigger list, and
  sharpen any that would no longer fire.

## How it reads

Two readers share the file: an agent loading it cold, and a human skimming to
see what the rules are now. Write for the skim (the human is the one who has to
spot that a rule has gone wrong).

- One rule per bullet, the rule itself first and the caveat after it, so the
  opening clause carries the meaning on its own.
- Bullets over paragraphs. Prose is for an idea that genuinely doesn't
  decompose, and it stays a few lines even then.
- An aside belongs in parentheses rather than behind a dash: "This thing is true
  (e.g. X Y Z)." over "This thing is true - e.g. X Y Z."
- Headings that name what they govern, so a reader can find the rule they came
  for and skip the rest.
- A concept's definition, rules and caveats sit under one heading, so reading
  one brings its neighbours. A new rule goes under the heading it belongs to
  rather than on the end of the file.
- Divide a section into `###` sub-headings once its bullets cover more than one
  topic, even when it fits on a screen, so a skim lands on the group it wants.
  Leave a few bullets on one topic undivided.
- A section grown past a screen is holding two topics. Split it and name both.
- Fold a one-bullet section into the section its topic belongs to, rather than
  giving a lone rule a heading of its own.
- Name everything in a code example after its role in the pattern
  (`myFunction`, `MyFunctionParams`, `myItems`), not after a real entity.
- Show a required argument in a documented command as `<name>`. Square brackets
  mean optional.

## Review

1. Read every entry in the path, the code, scripts and config it makes claims
   about, and the pointers into the path from files that load on their own.
2. Edit in this order: wrong, then misplaced, then duplicated or no longer
   needed, then overlong.
3. Apply the edits. Don't commit.

Done when every entry in the path has been judged and every edit matches the
rules above.

## Report

When another skill called this, use that skill's report. Otherwise report as
[shared/doc-report.md](../shared/doc-report.md) sets out.
