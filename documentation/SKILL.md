---
name: documentation
description: >-
  Updates agent docs and code comments after a piece of work (`AGENTS.md`, and
  comments in and around the diff), including the conventions behind the
  user's corrections to another author's branch. Use only when the user runs
  /documentation.
disable-model-invocation: true
---

# Documentation

Check whether the work just done should change the docs that guide future work.
Apply the ones that earn a place, and leave the working tree uncommitted.

This skill decides what to look at. What each edit says, and how each file is
judged, is the **Review** of the `review-docs` skill
([review-docs/SKILL.md](../review-docs/SKILL.md)) for agent docs and of the
`review-comments` skill ([review-comments/SKILL.md](../review-comments/SKILL.md)) for
comments, scoped to what's listed below rather than whole directories.

## Scope

- With nothing named, cover every comment, doc block and agent doc the branch
  adds or changes, plus those outside the diff the work has made stale, and
  every correction the user made to another author's work.
- A file or selection named alongside the command narrows the pass to that.

## Sources of signal

- **The diff** - the net diff vs the parent, gathered as
  [shared/net-diff.md](../shared/net-diff.md) sets out.
- **The session** - what had to be corrected, explained, or found out the hard
  way while doing the work: a convention you had to be told, a term that turned
  out ambiguous, a claim that proved false. A wrong turn taken because no doc
  recorded the convention is the strongest signal there is, and it never
  appears in the diff.
- **The corrections** - on another author's branch, the user's edits on top of
  their work. Each one that changes how the code is written is a convention the
  author wasn't steered to, and the net diff hides it, since only the corrected
  version survives. Worked through as **Corrections to another author's work**
  sets out.

## What to check

- The root and nested `AGENTS.md` covering the diff: a pattern they document
  that the work changed or retired.
- Agent docs the diff edits, reviewed whole, layout included: move a section
  down into the subfolder it governs, and sub-head a section holding more than
  one topic, as `review-docs`' **Where it goes** and **How it reads** set out.
- Agent docs read during the work, for their claims. The commands, scripts and
  stated invariants in files you're already reading go stale silently.
- Domain vocabulary: did a term turn out to mean two things, or get used two
  ways during the work?
- Comments and doc blocks, both inside the diff and the ones outside it the work
  has made stale. Hold each to `review-comments`' **How it reads** sentence by
  sentence, not only to whether it's true and earns its place: a pronoun that
  could point at two things, a reason given before what the code does, a doc
  block that leaves out what a caller needs (a call that does nothing in some
  case). Read the comments written this session cold, since their author already
  knows what each "this" means.

## Corrections to another author's work

Applies when another author wrote some of the branch. The aim is that the next
agent writing this kind of code writes the user's version first time.

- Take the user's edits as the `changes` skill
  ([changes/SKILL.md](../changes/SKILL.md)) finds them in **Find the author
  boundary** and **Diff the user's work only**, but diff the merged tree against
  the working tree rather than `HEAD` (`git diff -M <tree>`), so uncommitted
  fixes count. Its list of what to skip doesn't apply here: a style tweak is
  exactly the signal.
- Read each hunk as the author's version against the user's, and list every
  change to how the code is written rather than what it does (naming, shape,
  the helper or idiom used, where code lives, how a test is laid out). A fix to
  behaviour is out of scope unless a pattern caused it.
- For each, find what should have steered the author: the `AGENTS.md` files
  from the edited file's folder up to the root, and the docs they point at.
  Then take whichever case fits:
  - **Nothing states it** - write the rule, as `review-docs`' **New patterns**
    sets out.
  - **A doc states it, out of reach** - it doesn't load for that file, or sits
    behind a pointer that wouldn't fire. Move it or sharpen the pointer, as
    `review-docs`' **Where it goes** sets out, rather than adding a second copy.
  - **A doc states it loosely** - the author's version reads as following it.
    Reword it so only the user's version does.
  - **A doc states it plainly, where it loads** - leave it.
  - **It won't recur** - a call about this code alone. Leave it.
- Before writing a rule, check how the rest of the repo writes the same thing.
  Where it mostly follows the author's version, or you can't tell whether a
  correction is a convention or a one-off, ask the user rather than guess.

Done when every correction of how the code is written has one of those cases.

## Output

Report as [shared/doc-report.md](../shared/doc-report.md) sets out, agent docs and
comments in one list.

On another author's branch, add a list of every correction of how the code is
written, each with its case and the doc that now covers it, the ones left alone
included, so the user can check none was dropped.
