---
name: squash
description: >-
  Produce a squash commit extended description from the net branch diff vs its
  parent, as markdown ready to paste into GitHub's Extended description box. Use
  only when the user runs /squash.
disable-model-invocation: true
---

# Squash

Produce one markdown code block: the GitHub "Extended description" for a squash
merge. No title line (just the bullets).

## Source of truth

Gather the diff as [shared/net-diff.md](../shared/net-diff.md) sets out.

## Workflow

1. Confirm the current branch and that it has commits ahead of the parent.
2. Gather the diff, and read enough of it to understand the net end-state
   change.
3. Emit the code block below, with no other wrapping commentary beyond a brief
   lead-in if needed.

## Message shape

- No title line
- Dashed bullet list
- No backticks
- No inline file/function name lists, though naming one component or package is
  fine. Where several sites share a pattern, exemplify rather than enumerate
  ("services like OSRM and VROOM", not all of them)
- Laid out as [shared/output.md](../shared/output.md) sets out

## Content

- Describe the net end state vs parent, not the refactoring journey (no
  narrating intermediate churn). Three changes are worth naming the before-state
  for, because the before-state is the change: a relocation, stated as a move to
  its destination package, a replacement, stated as "replace X with Y", and a
  changed rule or constraint, stated as what happens now in place of what
  happened before ("refuse the delete instead of cascading the child")
- Open each bullet on the change itself (the verb, or the named thing doing it,
  "Replace ServerError with CodedError"), naming what it now uses rather than
  pointing at a rule ("use strict equality in place of ==", not "swap to the
  documented forms")
- Squash is a quick reference of the most important things that changed, not
  a second copy of the PR description. Name the new fields, relations,
  invariants and backfills that a later reader would miss. A batch of similar
  members (error codes, enum values) is one domain bullet, not a list of names.
  Where the members had different before-states, name both in that one bullet
  rather than splitting or dropping the odd one out.
- Can be shorter than feels complete. Drop tidying, styling polish, drive-by
  copy and a guard on a state that can't be reached, even though they're in the
  diff.
- Choose what earns a line by one test applied to every change, so two changes
  of the same kind never land on opposite sides of it. Where the whole PR is
  small tidies (a sweep bringing stray sites in line with documented rules),
  the tidies are the change, so detail every one, folding like ones into one
  bullet.
- The PR title is the squash commit's first line, so a bullet earns its place
  only by saying something the title doesn't. A title states the intent, so the
  bullet naming the mechanism - which relations, tables or fields changed, and
  where the new rule is enforced - still earns its place. Scale to the change:
  one bullet is a normal length for a small PR, and where the title covers the
  whole thing, say so and write none. A bigger PR summarises harder rather than
  growing a bullet per change: a whole feature still lands in well under ten,
  each naming a main piece a reader of the history needs, with the detail left
  to the PR description.
- Every squash bullet takes a tag. The known ones are `[Bug]`, `[Behaviour]`,
  `[Structure]`, `[Cleanup]`, `[Pattern]` and `[Docs]`. Keep them in the same
  order as the description sections. One tagged bullet per rule, not per
  sentence that states it: collapse same-tag lines that implement one change.
  Scale `[Pattern]` lines to the PR too: a PR introducing only a few gives each
  its own line, and one introducing many collapses them to a single line that
  says it introduces several and names the areas they cover, rather than
  picking a couple to stand for the rest. `[Docs]` earns a line
  only where the docs are the substantive change, so locating a pattern or
  adding a glossary entry gets none.
- If a leftover change does not fit those tags, stop and suggest a new
  tag. Do not invent one in the list, and do not leave the bullet
  unprefixed.
- Fold a supporting mechanism into the bullet it serves rather than giving it
  its own. A backfill of existing rows is its own change, not a mechanism of the
  write path.
- Bookkeeping the change forces (an entry in an exhaustive map, a registry, a
  generated file) follows from the change and isn't news. Say what a reader
  wouldn't assume instead.
- Fold what consumes a change into the bullet introducing it, as a short "along
  with" clause. New error codes and the front-end copy keyed off them are one
  bullet, not two.
- Keep a qualifying clause to a few words ("for front-ends" rather than "where
  all three apps read it")
- Omit irrelevant negatives

## Bullet length

- A bullet is one short sentence. Aim under 15 words and treat anything longer
  as a sign it's carrying a gloss or a second change.
- Don't explain a named thing once it's named ("leaving PaymentLine as the
  only connecting record", not "the only record of which payment settled an
  invoice").
- A replacement already says the old thing is gone, so don't trail it with what
  it retires.
- One clause after the hyphen at most. Never chain "which ... and what ... so
  ...", and never list the call sites that follow from a rule: state the rule
  and stop.
- When a bullet is too long, cut the reasoning and keep the change. Don't
  compress both halves into a denser sentence.

## Examples

```
- TextArea and TextAreaField both moved to @acme/components
- Text areas take a row count, defaulting to 3, in place of a pixel height
- The autosize hook is moved to @acme/hooks
- Character counts render as a badge that turns rose at the limit, enforced by truncating on parse
```

```
- Drop Invoice.paymentId and its foreign key, leaving PaymentLine as the only connecting record
- createPayment no longer connects the invoices it settles - an invoice counts as unpaid while it has no PaymentLine
- The dev migration scripts that predate the drop are pinned with @ts-nocheck rather than rewritten
```

```
- Replace ServerError with CodedError, a plain Error subclass that names itself and takes nothing but a code
- formatError recovers that code with unwrapResolverError and re-attaches it to extensions.code for front-ends
- Drops the messages payload (replaced in checkEnvVariables and where it was used to return settings.email)
```

Under the title "Stop a parent delete destroying financial and authored
records". The second bullet stands despite the title paraphrasing it, because it
names the mechanism, and it carries both before-states rather than splitting the
two relations that nulled from the six that cascaded.

```
- [Behaviour] Eight relations refuse a parent delete instead of cascading the child or nulling its link
- [Behaviour] Deleting an order with a payment or refund is refused, with new codes and admin copy
- [Pattern] Declare onDelete as Cascade, Restrict, or nothing
```
