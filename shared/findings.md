# Findings

How a local review takes its findings one fix at a time, every fix and every
commit waiting on the user's go-ahead. Pointed at, never copied.

## Before the first fix

Note any uncommitted changes already in the files in scope
(`git diff -- <paths>`), so no commit later sweeps them in with a finding's
fix.

## Lay out the findings

- One finding per problem, most consequential first. Track them with the todo
  tool: the findings are done when every one is ticked, not when the user
  stops asking.
- Simple stylistic fixes (a rename, a reordered import, a stale comment) are
  taken together as one finding rather than one each.
- List the findings in a line each, so the user sees the shape of what's
  coming before the first one opens. Then carry straight on to the first.

## Present the finding

- The problem: where (the file and function, with line numbers), what's wrong,
  and what it causes.
- The fix. Offer more than one only where they differ in something the user
  would weigh (behaviour, scope, a trade-off), with your recommendation first
  and why in a clause.
- Your questions, if you have any. Ask them with the finding and wait for the
  answers.
- Once there's nothing left to ask, confirm the fix you'll make and ask for the
  go-ahead. Wait for it, even where the fix looks obvious.

## Fix it on the go-ahead

- Make the agreed fix and nothing beyond it. Something else you spot while
  fixing becomes a new finding, not part of this one. The edits the fix forces
  elsewhere (updating the references to a rename, say) are part of it.
- Verify as [shared/verify.md](verify.md) sets out, running the suites
  that cover the logic when the fix changed behaviour.

## Offer the commit

- A fix that changed behaviour, or one large enough to review on its own: offer
  a commit for it now, with the title you'd use. Wait for the answer.
- A simple stylistic fix: hold it, and offer the held fixes as one commit at the
  end of the review.
- On a yes, commit only this finding's change, following the `commit` skill
  ([commit/SKILL.md](../commit/SKILL.md)). Where a file had changes before the
  review, stage the finding's hunks alone.
- On a no, leave it in the working tree.

## Next finding

Stay on a finding once its fix lands and its commit is offered, until the user
says they're done with it, since they may still be working on it in the tree.
A yes to the commit is that answer: once the commit lands, move straight on
without asking again.

Then tick the finding off and open the next one, saying which it is. Opening it
means presenting it as above and waiting for its go-ahead: a yes to one
finding's commit never covers the next one's fix. The user can reorder, merge
or drop the findings still to come.
