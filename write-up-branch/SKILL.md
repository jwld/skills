---
name: write-up-branch
description: >-
  Produce a PR title, AI Summary PR description, and squash commit extended
  description from the net branch diff vs its parent. Use when the user says
  "write up a branch", "write up this branch", or asks for a PR write-up based
  on the current branch.
---

# Write up a branch

Produce these outputs from the **net git diff vs the parent branch**, each in
its own markdown code block:

1. Suggested PR title
2. PR description
3. Squash commit message (extended description only) - skip when already in
   the PR description

## Source of truth

Gather the diff as [shared/net-diff.md](../shared/net-diff.md) sets out. The two
exceptions are a `## Bug:` section, which may quote logs pasted in the chat,
and the outstanding-work notice, which comes from the issue.

## Shared rules (every output)

- No hard-wrapped lines
- No redundant blank lines
- Short hyphens instead of em/en dashes
- Describe the net diff vs parent. State additions, removals, moves and
  replacements directly; exclude intermediate churn visible only in commits
- Omit irrelevant negatives
- Ignore pure docs-only noise unless docs are the substantive change
- Explain each fact once, in the section it belongs to. Another section that
  needs it gives only what it adds (Deploying says what to set, not again why
  the server won't boot without it)

## Workflow

1. Ask whether the write-up is for **human review** or **historical reference**.
   Do not continue until they answer. Historical-reference write-ups omit
   `## Suggested review order` and `## Review notes` (see Output 2).
2. Confirm the current branch and that it has commits ahead of the parent.
3. Check for uncommitted work (`git status`). If there is any, point it out and
   ask whether to commit it first with the `commit` skill
   ([commit/SKILL.md](../commit/SKILL.md)). Do not continue the write-up until they
   answer (either commit first, or they say to proceed without).
4. Gather the diff (see **Source of truth**), and read the issue
   (`gh issue view`) for its `## Outstanding` section.
5. Read enough of it to understand the net end-state change.
6. Emit the code blocks below, in order, with no other wrapping commentary
   beyond a brief lead-in if needed.

## Output 1 - PR title

A single-line suggested PR title in a markdown code block.

- Imperative, sentence case, no full stop
- Short enough to scan in a PR list (one clause, no trailing detail)
- In a repo with several apps, name the app a change confined to one of them
  lives in (`Convert the admin app SideNav to TS`), since a component name
  alone doesn't say which app's list it belongs to
- No `feat:` / `fix:` / scope prefixes

## Output 2 - PR description

Markdown code block starting with `## AI Summary` as the first heading, laid out
as below. Write a durable summary of the net diff rather than touring every
file. The reader can see individual lines, but should not have to reconstruct
the material changes from them later.

Find the issue as the `update-issue` skill's **Find the issue** step sets out
([update-issue/SKILL.md](../update-issue/SKILL.md)), and put `Resolves #123` on its own
line above `## AI Summary`. Never guess the number; leave it out when none
turns up. Where the issue's plan lists several PRs (`PR1:`, `PR2:`), only the
last one resolves it. An earlier one puts `Part of #123` there instead, so
merging it leaves the issue open.

When the issue's `## Outstanding` section holds an item for this PR (on an
issue spanning several PRs, one starting with the `PR2:` this PR has in the
plan), open the description with this notice, above the `Resolves` line:

```
> [!WARNING]
> Outstanding work on #123 has to land before this merges.
```

Give the notice only, never the items. They stay on the issue, which a long PR
can't bury, so there's one list to keep current. Leave the notice out once the
section holds nothing for this PR.

**Lead** - one sentence, no heading, sitting directly under `## AI Summary`.
What the branch changes. It has to stand alone, because it's what gets read in a
notification or quoted in a release note. Don't open with "This PR", and don't
spend a second sentence on why the work exists (the linked issue covers that).

**`## Suggested review order`** - only for human-review write-ups, straight
after the lead, since it's the part reviewers lean on most. The order to read
the change in, following the path the users take through the UI: whoever sets
the thing up first (an admin creating a record), then whoever meets it after.
Where the change has no UI path, follow its data from where it enters instead.
Give it as a numbered list, one step per user action, naming the component,
module or directory that step reads (`TaskForm`), never the files inside it,
which the reviewer finds on opening it.

Cover every group of changed files (a module, a migration, the specs beside
them), each named once at that level. Hang each group off the step that
reaches it, and gather what no step reaches (config, docs, tooling) in a
closing step, so a reviewer who follows the list has read the whole diff. Every
human-review write-up has this section, however small the diff. A change
confined to one component or file still gets one: a step per user action that
reaches it (an admin typing into the field, then the form that validates it),
naming the component each time.

**`## Bug: <what went wrong>`** - one section per bug the branch fixes,
whether that's why the PR exists or it was caught in passing. Bugs never share a
section, however small. Title each with the symptom in a few words
(`## Bug: Auto-renewal priced quantity fields at NaN`). Number them from 1 in
order of consequence only when there is more than one (`## Bug 1: ...`), and
give each two parts:

- `**Bug:**` - what went wrong, then the cause in a sentence, and any relevant
  logs pasted in the chat. Don't invent logs, and drop the ones that don't help.
- `**Fix:**` - how the branch fixes it: the mechanism, not a restatement of the
  symptom going away.

Name where each bug came from as a closing sentence of its `**Bug:**` part: the
commit or PR that introduced it, read off the history of the file that carried
the fault. Leave it out rather than guess at one. Where the fault sat latent
until a later commit made it reachable, name both and what had been masking it
until then, under a `### When it became reachable` subheading where that
outgrows a sentence.

**`## Behaviour changes`** - runtime or API semantics that differ from the
parent: a null that now disconnects, a miss that now throws, a field that
now clears. Give each one its own bullet. A change that is a fixed bug stays
in its `## Bug:` section, not here.

**`## Structure`** - a move, a rename that isn't the pattern itself, or new
surface that isn't a behaviour change or cleanup. Give each distinct
operation its own bullet and lead with a present-tense change verb such as
"Adds", "Removes", "Moves" or "Changes". Name the relevant symbol and code
boundary - including a file, function or resolver - when that is what makes
the scope precise. This is not a line-by-line file tour: omit bookkeeping
and group repeated edits that implement one rule.

On a feature-sized branch, keep what a later reader would search for (schema
changes, replaced API surface, moved modules) and drop the component-level
additions the behaviour bullets already imply.

Exclude motivation and extended justification; those belong in
`## Review notes`. Draft this section after the other change-kinds, and drop
any bullet that only restates a rule or change already under a `## Bug:`
section's fix, `## Behaviour changes`, `## Cleanup`, `## New patterns` or
`## Documentation`.

Prefer:

- Moves `statementDescriptor` from billing constants to Stripe constants
- Adds a `CardPayment.statementDescriptor` resolver that falls back to the
  constant while the stored descriptor is null

over broad outcome bullets such as "Stripe reports own the persisted
descriptor", which hide the actual changes a reader needs to recover later.

**`## Cleanup`** - unused surface removed while the work was in the area: a
mutation with no consumer, an orphaned input, a helper nobody imports. Not
every deletion belongs here, only one that existed solely because nothing
had deleted it yet.

**`## New patterns`** - when the PR introduces a new pattern. Give each one a
numbered rule the next author can follow rather than infer from a single call
site. The numbers keep the section separable as it grows, and the rules stay
full sentences rather than shrinking to labels. Phrase each as an instruction
("Declare...", "Keep...") rather than a description of what the branch does.

Follow a pattern that governs syntax with the shortest snippet of what it
rejects and what it wants, in one fenced block:

```ts
// Wrong
prisma.myModel.update(args as Prisma.MyModelUpdateArgs)

// Right
prisma.myModel.update({ data: { myField: data.myField }, where })
```

Cut everything the rule doesn't turn on - no imports, no surrounding function,
and only the one field or argument it takes to show the difference. A pattern
about naming or placement has nothing to show, so its rule stands alone.

**`## Documentation`** - documentation the branch adds or changes: a rule
written into `AGENTS.md`, a glossary entry, a doc block that records a
constraint. One bullet each, naming the file. A rule already given under
`## New patterns` earns a bullet here only for where it's now written down, so
docs that only record those patterns share one bullet naming the files. Give
the smaller conventions picked up along the way a clause, not a list.

**`## Review notes`** - only for human-review write-ups. Only what needs a
human: the change most likely to be wrong, a decision that had a plausible
alternative and why this one won, anything you're unsure of. Keep each note to
a few sentences, and give parallel items (several decisions) a nested list
rather than a paragraph chained with "And...". Omit the section when the
write-up is for historical reference, or when the branch really is mechanical.

**`## Deploying`** - only when whoever merges has to do something: a new env
var, a flag to flip, a manual script to run, an ordering constraint against
another PR. A schema migration that ships with the deploy doesn't count, nor
does an ordering constraint that's already been satisfied.

**`## Squash message`** - last section. Follow the `squash` skill
([squash/SKILL.md](../squash/SKILL.md)) for the bullets (reuse the diff already
gathered above) so whoever merges has them to hand. Put them in a fenced code
block, so GitHub renders a copy button and they can be lifted in one click.
When emitting output 2 as a code block in chat, fence that outer block with
four backticks so the inner fence survives.

The squash skill owns the tags, the folding and what earns a line. Follow it
rather than mirroring every description item, and hold it to that skill's size
limit (well under ten bullets for a whole feature) however long the
description above it runs.

Rules:

- Omit any section with nothing to say. Never write a heading followed by "N/A"
  or "none"
- Never fill a section with a consequence that follows by definition from a
  change already listed. That a removed field now errors for anything still
  selecting it is the removal, not a behaviour change
- No Testing section that just says tests pass
- Shorter than feels complete. Every bullet a reviewer skips makes the next one
  likelier to be skipped too
- Scale to the number of changes, not the size of the diff. A section that
  would pass six bullets belongs to a feature-sized branch: group it by product
  area (sign-in, checkout, emails), one bullet per area naming the outcome and
  its main pieces, and leave the per-change detail to the diff

## Output 3 - Squash commit message

Skip when the squash message has already been set in the PR description.

Otherwise follow the `squash` skill ([squash/SKILL.md](../squash/SKILL.md)). Reuse
the diff already gathered above rather than re-running its workflow from
scratch. Emit its code block as the third output.
