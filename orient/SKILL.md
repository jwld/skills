---
name: orient
description: >-
  Orients a reader on a branch before they read it (a lead paragraph, then a
  sized list of the areas of logic it changes). Use only when the user runs
  /orient.
disable-model-invocation: true
---

# Orient

Where the weight of a branch sits, for someone about to read or review it.
Orientation only: it says what the branch does and where the work went, and
leaves explaining the code to whatever comes next.

## Scope

- Work from the net diff vs the parent, gathered as
  [shared/net-diff.md](../shared/net-diff.md) sets out. The per-file line counts in
  `--stat` are what size the areas, so keep them to hand.
- Fetch the connected PR for the link:
  `gh pr view --json number,title,url,baseRefName`.

## Output

Prose in complete sentences, laid out as
[shared/output.md](../shared/output.md) sets out. No bullet list of paths.

**Lead** - one short paragraph naming the PR as a link, the base branch, and the
scale of the change. Say what the branch does in a sentence.

**Areas** - one line per area of logic the branch adds or reworks (a new modal,
notification, resolver etc.), largest first, each naming the area, saying what
it is in a clause, and giving roughly how much of the diff it accounts for. The
reader wants to know what they're in for and where the weight sits, so don't
explain an area here.

- Group by area of logic rather than by file. An area usually spans several
  files, and one large file can serve two areas.
- Size the areas from the stat and round hard ("about a third", "a couple of
  hundred lines"). A rough share honestly labelled is worth more than a
  precise-looking number the line counts don't support.
- Keep generated and mechanical churn out of the percentages (lockfiles,
  snapshots, pure renames) and account for it in a single line, so it doesn't
  drown the real work.
- Say so when the balance is surprising: the headline feature turning out to be
  the smallest area, or most of the diff sitting in something the PR description
  never mentions.
