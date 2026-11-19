---
name: changes
description: >-
  Summarises the changes the user made on the current branch on top of another
  author's work, as markdown ready to paste into a PR comment. Use only when the
  user runs /changes.
disable-model-invocation: true
---

# Summarise changes

## Find the author boundary

The user is doing a final pass over work the other author has finished, so their
commits sit at the end of the branch.

1. `git log --format='%h | %an | %ad | %s' --date=short origin/<base>..HEAD`,
   with the branch's base as [shared/base-branch.md](../shared/base-branch.md)
   sets out.
2. The other author's most recent commit is the boundary.
3. If every commit is the user's, summarise `origin/<base>...HEAD` and say so.
   If none are, say so and stop.
4. If any of the user's own commits sit below the boundary, the two authors have
   interleaved and the diff below would drop that earlier work. Say so rather
   than summarising as if it were complete.

## Diff the user's work only

Base merges on the branch would otherwise be attributed to the user, so
compare against the other author's work with the base folded in:

```bash
git merge-tree --write-tree origin/<base> <boundary>   # prints a tree oid
git diff --stat -M <tree> HEAD
```

- `merge-tree` exits 1 on conflicts but still writes the tree. Conflicted paths
  come back with markers in the diff, so read those files directly instead.
- Work from the stat to find the themes, then read the diffs that carry real
  behaviour.
- Skip nits: formatting, unused-field drops, validation reorders, error-code
  enums, small style tweaks, and UI polish.
- Keep new fields, new guards, and behaviour the other author would hit if they
  kept working on this (e.g. an `isPaid` field that now drives reset and
  read-only).
- Skip doc edits unless they are a new file or a large rewrite of an existing
  one.

## Output

One fenced markdown block, so the whole thing can be copied in one go. The
reader is the other author, coming back to work they considered finished and
being asked to accept edits to it, so answer their questions in the order
they'll ask them.

**Heading** - a single `##` line crediting the other author and framing what
follows as work on top of theirs.

**Lead** - one sentence directly under it, no heading of its own. What was
changed and why, enough that they can stop there and know whether they need the
detail.

**`### Departures from <author>'s approach`** - only where their decision was
reversed, their code deleted, or their approach replaced. A new guard or field
in front of their logic is not a departure. Omit the section when nothing of
theirs was undone.

**`### Added on top`** - new work that sits on their logic: fields, guards,
locks, conversions. Keep it short, but include anything they would need to know
to keep working here. Omit when there is nothing beyond the lead.

Rules:

- Glanceable. Short bullets, no restating the lead, no "worth a look" or "you
  can push back" asides.
- Conceptual: no per-file lists, no paths, and no line counts unless the scale
  is the point
- Describe each change against their version, so renames, moves and deletions
  are all fair game. `write-up-branch` works from the net diff vs the parent;
  this works from the diff against their work
- Neutral register throughout. It describes someone's work to their face, so say
  what changed rather than that it's now better (no "fixed", "cleaned up",
  "simplified", "improved")
- No hard-wrapped lines, and short hyphens rather than em dashes
- Claim only what the diff shows

After the block, outside it:

- Anything uncertain or deliberately left out
- How the range was scoped, so the attribution can be sanity checked
